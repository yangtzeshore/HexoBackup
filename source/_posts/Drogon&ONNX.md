---
title: 打造 C++ 高性能 AI 推理服务：Drogon + ONNX Runtime 
date: 2025-12-09 10:09:55
tags:
- AI Drogon Onnx Cxx OpenCV
categories:
- Onnx Drogon
---



## 打造 C++ 高性能 AI 推理服务：Drogon + ONNX Runtime (YOLOv5 GPU)

在构建需要高并发、低延迟的深度学习推理服务时，性能至关重要。本项目展示了如何使用 C++ 的 **Drogon** 异步 Web 框架和 **ONNX Runtime (ORT)**，搭建一个基于 **YOLOv5 模型**的 **GPU 加速**目标检测 HTTP 服务。该架构完美结合了高性能 I/O 和计算密集型任务的并行处理。源码会在文章最后展示出来。github地址：https://github.com/yangtzeshore/Drogon-ONNX。

------

### 核心技术栈概览

本项目是典型的 C++ 高性能服务架构，融合了以下关键组件：

1. **Drogon (Web 框架):** C++ 的高性能 Web 框架，采用**非阻塞 I/O** 模型，擅长处理高并发网络连接。
2. **ONNX Runtime (ORT):** 微软推出的跨平台机器学习推理引擎，支持多种加速硬件，本项目中配置了 **CUDA Execution Provider** 实现 **GPU 加速**。
3. **OpenCV:** 用于图像的读取、解码、预处理（如 Letterbox 缩放）以及后处理（如 NMS 非极大值抑制）。
4. **nlohmann/json:** 轻量级的 C++ JSON 库，用于构造标准的 API 响应。

------

### 架构设计：I/O 与计算分离 (PredictController)

高并发服务的常见瓶颈在于 I/O (网络、文件读写) 和 CPU/GPU 计算 (图像解码、模型推理) 相互阻塞。本项目通过 **Drogon 的异步特性**和 **C++ `std::async`** 实现了 I/O 与计算的彻底分离。

#### 1. 异步 I/O 线程 (Drogon Event Loop)

Drogon 的 I/O 线程（Event Loop）负责：

- 接收 HTTP 请求。
- 解析 `multipart/form-data` 请求体，获取上传的图片文件内容。
- **并发控制：** 检查当前的活跃推理任务数量 (`active_tasks`)。如果达到设定的上限 (`MAX_PARALLEL_INFERENCE = 4`)，立即返回 `503 Service Unavailable` 状态码，保护服务不过载。
- **任务投递：** 使用 `std::async(std::launch::async, ...)` 将计算密集型的任务**立即**投递到系统线程池（或计算线程池）中执行，从而**不阻塞**当前的 I/O Event Loop。

#### 2. 独立计算线程 (System Thread Pool)

被 `std::async` 唤起的线程负责所有的计算工作：

- **图片解码：** 使用 `cv::imdecode` 将二进制文件内容解码为 `cv::Mat`。
- **模型推理：** 调用 `InferenceManager::infer()` 完成预处理、GPU 推理和后处理。
- **构造响应：** 将推理结果封装成 JSON 格式。
- **发送响应：** 通过 `callback(resp)` 将响应发送回 Drogon I/O 线程，由 I/O 线程完成网络发送。
- **计数器维护：** 推理完成后，将活跃任务计数器 `active_tasks` 减一。

------

### 模型管理与 GPU 加速 (InferenceManager)

`InferenceManager` 是项目的核心计算模块，负责模型的加载和推理流程。

#### 1. 强制 GPU 加载

在 `loadModel` 方法中，通过配置 `OrtCUDAProviderOptions`，强制 **ONNX Runtime** 使用 **CUDA Execution Provider**。如果 CUDA 无法配置成功，程序将直接终止（`LOG_FATAL`），确保服务只运行在 GPU 加速模式下，防止在生产环境中意外降级到性能较低的 CPU 模式。

C++

```
// 关键配置：强制使用 CUDA
OrtCUDAProviderOptions cuda_options;
cuda_options.device_id = 0; // 使用 0 号显卡
session_options.AppendExecutionProvider_CUDA(cuda_options);
```

#### 2. 完整的 YOLOv5 推理流程

`infer` 函数封装了目标检测的全部三个步骤：

- **预处理 (`preprocess`):** 接收原始图像，执行 **Letterbox** (保持宽高比缩放) 到 $640 \times 640$，然后进行 **HWC -> CHW** 布局转换和 **$0-1$ 归一化**。
- **模型运行 (`session_->Run`):** 将预处理后的 `float` 数组包装成 `Ort::Value`，投递给 GPU 进行推理。
- **后处理 (`postprocess`):** 解析 ONNX Runtime 返回的原始张量 (`[1, 25200, 85]`)，计算最终的置信度，执行 **NMS (非极大值抑制)**，并将边界框坐标**还原**到原始图像尺寸。

------

###  C++ 标准和构建配置 (CMakeLists.txt)

项目的构建配置文件 `CMakeLists.txt` 体现了对现代 C++ 特性和依赖项的严格要求：

- **C++ 标准检测:** 项目会检查编译器对 `any`, `string_view`, `coroutine` 等 C++20 特性的支持，并优先使用 **C++20**，否则退回到 **C++17**。最低要求为 C++17，以确保现代特性和库的兼容性。
- **核心依赖项:** 项目明确地使用了 Drogon、`nlohmann_json`、OpenCV 和 **`onnxruntime`** 四个关键库，通过 `target_link_libraries` 链接到主程序。

------

### 性能压测与优化

项目通过设置 **`MAX_PARALLEL_INFERENCE`** (在 `PredictController.cc` 中为 4) 实现了服务端限流。这能有效防止计算线程池过载，保持服务稳定。

配合 `wrk` 压测工具和提供的 `post_image.lua` 脚本，您可以轻松地对服务进行负载测试，验证其在 GPU 加速下的高吞吐量表现：

Bash

```
# 示例压测命令
wrk -t12 -c100 -d30s -s post_image.lua http://localhost:8080/api/predict
```

通过这种 **I/O 异步 + 计算并行 + GPU 加速**的架构，本项目成功构建了一个高性能、高并发的 C++ 深度学习推理服务，是生产环境中部署 AI 模型的理想选择。

------

### 源码介绍

#### 代码架构

* **project**
    * build/
    * **controllers/**
        * PredictController.cc
        * PredictController.h
    * filters/
    * models/
    * onnxruntime_lib/
    * plugins/
    * test/
    * views/
* CMakeLists.txt
* config.json
* config.yaml
* InferenceManager.cc
* InferenceManager.h
* main.cc

上述drogon的前后端代码，可以自行删减，本文专注于核心业务框架，多余的文件如果没有，不影响整体的运行和压测。onnxruntime_lib需要自己下载解压放到目录，也可以自行解压修改cmake文件。



CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.5)
project(drogon_project CXX)

include(CheckIncludeFileCXX)

check_include_file_cxx(any HAS_ANY)
check_include_file_cxx(string_view HAS_STRING_VIEW)
check_include_file_cxx(coroutine HAS_COROUTINE)
if (NOT "${CMAKE_CXX_STANDARD}" STREQUAL "")
    # Do nothing
elseif (HAS_ANY AND HAS_STRING_VIEW AND HAS_COROUTINE)
    set(CMAKE_CXX_STANDARD 20)
elseif (HAS_ANY AND HAS_STRING_VIEW)
    set(CMAKE_CXX_STANDARD 17)
else ()
    set(CMAKE_CXX_STANDARD 14)
endif ()

set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(ORT_HOME "${CMAKE_SOURCE_DIR}/onnxruntime_lib")
include_directories(${ORT_HOME}/include)
link_directories(${ORT_HOME}/lib)
add_executable(${PROJECT_NAME} main.cc InferenceManager.cc)

# ##############################################################################
# If you include the drogon source code locally in your project, use this method
# to add drogon 
# add_subdirectory(drogon) 
# target_link_libraries(${PROJECT_NAME} PRIVATE drogon)
#
# and comment out the following lines

find_package(Drogon CONFIG REQUIRED)
find_package(nlohmann_json REQUIRED)
find_package(OpenCV REQUIRED)

target_link_libraries(${PROJECT_NAME} 
    PRIVATE Drogon::Drogon 
    nlohmann_json::nlohmann_json 
    onnxruntime 
    ${OpenCV_LIBS}
)

# ##############################################################################

if (CMAKE_CXX_STANDARD LESS 17)
    message(FATAL_ERROR "c++17 or higher is required")
elseif (CMAKE_CXX_STANDARD LESS 20)
    message(STATUS "use c++17")
else ()
    message(STATUS "use c++20")
endif ()

aux_source_directory(controllers CTL_SRC)
aux_source_directory(filters FILTER_SRC)
aux_source_directory(plugins PLUGIN_SRC)
aux_source_directory(models MODEL_SRC)

drogon_create_views(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/views
                    ${CMAKE_CURRENT_BINARY_DIR})
# use the following line to create views with namespaces.
# drogon_create_views(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/views
#                     ${CMAKE_CURRENT_BINARY_DIR} TRUE)
# use the following line to create views with namespace CHANGE_ME prefixed
# and path namespaces.
# drogon_create_views(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/views
#                     ${CMAKE_CURRENT_BINARY_DIR} TRUE CHANGE_ME)

target_include_directories(${PROJECT_NAME}
                           PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}
                                   ${CMAKE_CURRENT_SOURCE_DIR}/models)
target_sources(${PROJECT_NAME}
               PRIVATE
               ${SRC_DIR}
               ${CTL_SRC}
               ${FILTER_SRC}
               ${PLUGIN_SRC}
               ${MODEL_SRC})
# ##############################################################################
# uncomment the following line for dynamically loading views 
# set_property(TARGET ${PROJECT_NAME} PROPERTY ENABLE_EXPORTS ON)

# ##############################################################################

add_subdirectory(test)
```

main.cc

```cpp
#include <drogon/drogon.h>
#include "InferenceManager.h"

int main() {
    const std::string MODEL_PATH = "/app/cxx/yolov5/yolov5s.onnx"; 
    
    // 1. 加载模型 (耗时操作，启动前完成)
    InferenceManager::getInstance().loadModel(MODEL_PATH);
    
    // 2. 获取 CPU 核心数，配置并发参数
    unsigned int num_cores = std::thread::hardware_concurrency();
    
    // 设置 I/O 线程数。
    // 作为一个计算密集型服务，I/O 线程不需要太多，核心数的一半或者固定 4-8 个通常足够。
    // 剩下的算力留给 compute threads。
    drogon::app().setThreadNum(num_cores > 0 ? num_cores : 4);
    
    // 设置客户端最大连接数，防止服务过载
    drogon::app().setClientMaxBodySize(10 * 1024 * 1024); // 10MB
    
    drogon::app().addListener("0.0.0.0", 8080);
    LOG_INFO << "Server starting with " << drogon::app().getThreadNum() << " threads.";
    
    drogon::app().run();
    return 0;
}
```

InferenceManager.h

```c++
// InferenceManager.h
#pragma once
#include <onnxruntime_cxx_api.h>
#include <opencv2/opencv.hpp> 
#include <string>
#include <memory>
#include <vector>

// 定义检测结果结构体
struct Detection {
    int class_id;       // 类别ID
    float confidence;   // 置信度
    cv::Rect box;       // 边界框 (x, y, width, height)
    std::string className; // 类别名称 (可选，如果映射表存在)
};

class InferenceManager {
private:
    std::unique_ptr<Ort::Session> session_;
    Ort::Env env_;
    Ort::MemoryInfo memory_info_;
    
    // 模型参数 (YOLOv5s 默认)
    const int input_width_ = 640;
    const int input_height_ = 640;
    const float conf_threshold_ = 0.45f; // 置信度阈值
    const float iou_threshold_ = 0.50f;  // NMS 阈值
    
    // 类别名称列表 (COCO 数据集 80类)
    std::vector<std::string> class_names_ = {
        "person", "bicycle", "car", "motorcycle", "airplane", "bus", "train", "truck", "boat", "traffic light",
        "fire hydrant", "stop sign", "parking meter", "bench", "bird", "cat", "dog", "horse", "sheep", "cow",
        // ... (你需要补全 COCO 80类，或者加载你的自定义类别)
        // 为了演示简略，这里只列出部分
    };

    InferenceManager();

    // 内部辅助函数
    std::vector<float> preprocess(const cv::Mat& image, float& ratio, int& dw, int& dh);
    std::vector<Detection> postprocess(const std::vector<float>& output, const std::vector<int64_t>& shape, float ratio, int dw, int dh);

public:
    static InferenceManager& getInstance() {
        static InferenceManager instance;
        return instance;
    }

    void loadModel(const std::string& model_path);

    // 接口直接接收 OpenCV 图像，返回结构化的检测结果
    std::vector<Detection> infer(const cv::Mat& input_image);
};
```

InferenceManager.cc

```c++
// InferenceManager.cc
#include "InferenceManager.h"
#include <drogon/drogon.h>

InferenceManager::InferenceManager() 
    : env_(ORT_LOGGING_LEVEL_WARNING, "AITrackingService"),
      memory_info_(Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault)) {}

void InferenceManager::loadModel(const std::string& model_path) {
    LOG_INFO << "Starting ONNX Runtime model loading (GPU Mode)...";
    Ort::SessionOptions session_options;

    // 1. 依然保持低 CPU 占用，把 CPU 留给图片解码和网络 IO
    session_options.SetIntraOpNumThreads(1); 
    session_options.SetGraphOptimizationLevel(GraphOptimizationLevel::ORT_ENABLE_ALL);

    // 2. 强制配置 CUDA
    OrtCUDAProviderOptions cuda_options;
    cuda_options.device_id = 0; // 使用 0 号显卡
    // cuda_options.arena_extend_strategy = 0; // 可选：显存策略
    // cuda_options.gpu_mem_limit = 2 * 1024 * 1024 * 1024; // 可选：限制显存占用 2GB

    try {
        session_options.AppendExecutionProvider_CUDA(cuda_options);
    } catch (const Ort::Exception& e) {
        LOG_FATAL << "Failed to configure CUDA provider: " << e.what();
        // 既然是为了跑 GPU，如果 CUDA 失败，应该直接终止程序，而不是降级到 CPU
        throw; 
    }

    session_ = std::make_unique<Ort::Session>(env_, model_path.c_str(), session_options);
    LOG_INFO << "Model loaded successfully on GPU!";
}

// 1. 预处理：Letterbox (保持宽高比缩放) + HWC -> CHW + 归一化
std::vector<float> InferenceManager::preprocess(const cv::Mat& img, float& ratio, int& dw, int& dh) {
    // Step 1: Letterbox resize
    // 目的是把图片缩放到 640x640，同时保持长宽比，不足的地方填充灰色
    int w = img.cols;
    int h = img.rows;
    float scale = std::min((float)input_width_ / w, (float)input_height_ / h);
    ratio = scale; // 记录缩放比例，用于后续还原坐标
    
    int new_w = std::round(w * scale);
    int new_h = std::round(h * scale);
    
    dw = (input_width_ - new_w) / 2;
    dh = (input_height_ - new_h) / 2;

    cv::Mat resized_img;
    cv::resize(img, resized_img, cv::Size(new_w, new_h));
    
    // 填充边界
    cv::Mat padded_img;
    cv::copyMakeBorder(resized_img, padded_img, 
        dh, input_height_ - new_h - dh, 
        dw, input_width_ - new_w - dw, 
        cv::BORDER_CONSTANT, cv::Scalar(114, 114, 114)
    );

    // Step 2: HWC -> CHW, BGR -> RGB, Normalize 0-1
    // ONNX Runtime 需要 float 数组，并且维度顺序是 [Batch, Channels, Height, Width]
    std::vector<float> input_tensor_values;
    input_tensor_values.resize(1 * 3 * input_height_ * input_width_);

    // 归一化并改变布局
    // 这是一个相对耗时的操作，OpenCV 的 blobFromImage 也可以做，但为了展示细节我们手动写
    for (int c = 0; c < 3; c++) {
        for (int i = 0; i < input_height_; i++) {
            for (int j = 0; j < input_width_; j++) {
                // padded_img 是 BGR，我们需要 RGB (所以 c=0 取 B->2, c=1 取 G->1, c=2 取 R->0 ?)
                // YOLOv5 通常使用 RGB 训练。OpenCV 默认是 BGR。
                // 像素值索引: (i * input_width_ + j) * 3 + (2 - c) 
                // 2-c 实现了 BGR 到 RGB 的转换
                float val = padded_img.at<cv::Vec3b>(i, j)[2 - c]; 
                input_tensor_values[c * input_height_ * input_width_ + i * input_width_ + j] = val / 255.0f;
            }
        }
    }
    return input_tensor_values;
}

// 2. 后处理：解析 YOLO 输出 -> NMS -> 还原坐标
std::vector<Detection> InferenceManager::postprocess(const std::vector<float>& output, 
    const std::vector<int64_t>& shape, float ratio, 
    int dw, int dh) {

    std::vector<int> class_ids;
    std::vector<float> confidences;
    std::vector<cv::Rect> boxes;

    // Output Shape: [1, 25200, 85] (Batch, Anchors, xywh+obj+classes)
    int anchors = shape[1]; // 25200
    int dimensions = shape[2]; // 85

    // 遍历每一个 Anchor
    for (int i = 0; i < anchors; ++i) {
        const float* row_ptr = output.data() + i * dimensions;
        
        float obj_conf = row_ptr[4]; 
        if (obj_conf < conf_threshold_) continue;

        // 找出类别得分最高的
        float max_class_score = 0;
        int max_class_id = -1;
        for (int j = 5; j < dimensions; ++j) {
            if (row_ptr[j] > max_class_score) {
                max_class_score = row_ptr[j];
                max_class_id = j - 5;
            }
        }

        // 最终得分 = 有物体概率 * 属于某类别的概率
        float final_score = obj_conf * max_class_score;
        if (final_score >= conf_threshold_) {
            // 解析坐标 (cx, cy, w, h) -> 相对于 640x640 模型输入的
            float cx = row_ptr[0];
            float cy = row_ptr[1];
            float w = row_ptr[2];
            float h = row_ptr[3];

            // 还原坐标到原图
            // 1. 去掉 padding
            float r_cx = (cx - dw) / ratio;
            float r_cy = (cy - dh) / ratio;
            float r_w = w / ratio;
            float r_h = h / ratio;

            // 2. 转为左上角 x, y
            int left = (int)(r_cx - r_w / 2);
            int top = (int)(r_cy - r_h / 2);
            int width = (int)r_w;
            int height = (int)r_h;

            class_ids.push_back(max_class_id);
            confidences.push_back(final_score);
            boxes.push_back(cv::Rect(left, top, width, height));
        }
    }

    // NMS (非极大值抑制) - 去重
    std::vector<int> indices;
    cv::dnn::NMSBoxes(boxes, confidences, conf_threshold_, iou_threshold_, indices);

    std::vector<Detection> results;
    for (int idx : indices) {
        Detection det;
        det.class_id = class_ids[idx];
        det.confidence = confidences[idx];
        det.box = boxes[idx];
        if(det.class_id < class_names_.size()) {
            det.className = class_names_[det.class_id];
        } else {
            det.className = "unknown";
        }
        results.push_back(det);
    }
    return results;
}

std::vector<Detection> InferenceManager::infer(const cv::Mat& input_image) {
    if (!session_ || input_image.empty()) return {};

    float ratio;
    int dw, dh;
    
    // 1. 预处理
    std::vector<float> input_data = preprocess(input_image, ratio, dw, dh);
    std::vector<int64_t> input_shape = {1, 3, input_height_, input_width_};

    // 2. ONNX Runtime 推理
    Ort::Value input_tensor = Ort::Value::CreateTensor<float>(
        memory_info_, input_data.data(), input_data.size(), input_shape.data(), input_shape.size()
    );

    const char* input_names[] = {"images"}; 
    const char* output_names[] = {"output0"}; 

    try {
        auto output_tensors = session_->Run(
            Ort::RunOptions{nullptr}, input_names, &input_tensor, 1, output_names, 1
        );

        float* floatarr = output_tensors[0].GetTensorMutableData<float>();
        auto output_info = output_tensors[0].GetTensorTypeAndShapeInfo();
        std::vector<int64_t> output_shape = output_info.GetShape();
        
        std::vector<float> raw_output(floatarr, floatarr + output_info.GetElementCount());

        // 3. 后处理
        return postprocess(raw_output, output_shape, ratio, dw, dh);

    } catch (const Ort::Exception& e) {
        LOG_ERROR << "Inference Failed: " << e.what();
        return {};
    }
}
```

PredictController.h

```
#pragma once
#include <drogon/HttpSimpleController.h>

using namespace drogon;

class PredictController : public drogon::HttpSimpleController<PredictController>
{
  public:
    void asyncHandleHttpRequest(const HttpRequestPtr& req, std::function<void (const HttpResponsePtr &)> &&callback) override;
    
    PATH_LIST_BEGIN
    // 绑定到 POST /api/predict
    PATH_ADD("/api/predict", Post);
    PATH_LIST_END
};

```

PredictController.cc

```
#include "PredictController.h"
#include "../InferenceManager.h"
#include <nlohmann/json.hpp>
#include <opencv2/opencv.hpp>
#include <drogon/drogon.h>
#include <future> // 引入 std::async

using json = nlohmann::json;

// 在类中定义一个 atomic 计数器
std::atomic<int> active_tasks{0};
const int MAX_PARALLEL_INFERENCE = 4; // 根据显存大小决定

void PredictController::asyncHandleHttpRequest(const HttpRequestPtr& req, std::function<void (const HttpResponsePtr &)> &&callback)
{
    // 1. 解析 Multipart (I/O 线程)
    drogon::MultiPartParser fileParser;
    if (fileParser.parse(req) != 0 || fileParser.getFiles().empty()) {
        auto resp = HttpResponse::newHttpResponse();
        resp->setStatusCode(k400BadRequest);
        resp->setBody("Please upload an image file");
        callback(resp);
        return;
    }

    if (active_tasks >= MAX_PARALLEL_INFERENCE) {
        auto resp = HttpResponse::newHttpResponse();
        resp->setStatusCode(k503ServiceUnavailable); // 告诉压测工具：我过载了
        callback(resp);
        return;
    }
    active_tasks++;

    auto& imageFile = fileParser.getFiles()[0];
    auto fileContent = std::make_shared<std::vector<char>>(
        imageFile.fileData(), 
        imageFile.fileData() + imageFile.fileLength()
    );

    // 2. 使用 std::async 投递到系统线程池进行 CPU 密集计算
    // std::launch::async 确保立即在另一个线程中运行，不阻塞当前的 I/O Event Loop
    std::async(std::launch::async, [fileContent, callback = std::move(callback)]() {
        try {
            // --- 计算线程开始 ---
            
            // 解码
            cv::Mat img = cv::imdecode(*fileContent, cv::IMREAD_COLOR);
            if (img.empty()) {
                auto resp = HttpResponse::newHttpResponse();
                resp->setStatusCode(k400BadRequest);
                resp->setBody("Failed to decode image");
                callback(resp);
                return;
            }

            auto start_time = std::chrono::steady_clock::now();
            // 推理
            auto results = InferenceManager::getInstance().infer(img);
            auto end_time = std::chrono::steady_clock::now();
            auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(end_time - start_time).count();
            LOG_DEBUG << "Inference completed in " << duration << "ms";

            // 构造结果
            json response_json;
            response_json["status"] = "success";
            json detections = json::array();
            for(const auto& det : results) {
                json j_det;
                j_det["label"] = det.className;
                j_det["confidence"] = det.confidence;
                j_det["box"] = {{"x", det.box.x}, {"y", det.box.y}, {"w", det.box.width}, {"h", det.box.height}};
                detections.push_back(j_det);
            }
            response_json["predictions"] = detections;

            auto resp = HttpResponse::newHttpResponse();
            resp->setContentTypeCode(CT_APPLICATION_JSON);
            resp->setBody(response_json.dump());
            
            // 异步发送响应
            callback(resp);
            
            // --- 计算线程结束 ---
        } catch (const std::exception& e) {
            auto resp = HttpResponse::newHttpResponse();
            resp->setStatusCode(k500InternalServerError);
            resp->setBody(e.what());
            callback(resp);
        }
        
        active_tasks--;
    });
}
```



最后给出本项目的压测文件，也可以参考下：

post_image.lua

```lua
-- wrk -t12 -c100 -d30s -s post_image.lua http://localhost:8080/api/predict
wrk.method = "POST"
wrk.headers["Content-Type"] = "multipart/form-data; boundary=---------------------------7d44e178b0434"

-- 注意：这里需要你提前准备好一个包含 multipart 格式的二进制文件 body.bin
-- 或者在 Lua 中拼接，但最快的方法是读取预先录制好的 body 文件
local f = io.open("test_image_multipart.bin", "rb")
local content = f:read("*all")
f:close()

wrk.body = content

```



------

### 延伸阅读

- **Drogon 官方文档：** 了解更多异步编程和控制器模式。
- **ONNX Runtime 文档：** 深入了解 CUDA Execution Provider 的配置选项。
