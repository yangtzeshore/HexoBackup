---
title: 一次简单的Onnx和OpenCV的实验
date: 2025-11-29 11:08:55
tags:
- AI OpenCV Onnx Cxx 
categories:
- Onnx 
---



因为业务要求，最近部署了很多小模型（纯cpu版本），其中涉及到的就是图像处理比较多，基本框架和思路，依然是onnx和opencv的组合。然后，这篇博客主要是记录一下开发的骨架，其中的业务相关的代码剔除了，可以给后续需要的时候快速开发、参考。



### 代码结构

------

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <cmath>
#include <algorithm>
#include <opencv2/opencv.hpp>

// 定义一些常量，模拟模型需要的输入参数
const int INPUT_WIDTH = 224;
const int INPUT_HEIGHT = 224;
const int INPUT_CHANNELS = 3;

// ImageNet 的常见均值和方差 (RGB顺序)
const float MEAN[] = {0.485f, 0.456f, 0.406f};
const float STD[]  = {0.229f, 0.224f, 0.225f};

// --- 1. 预处理函数 (核心难点) ---
std::vector<float> preprocess(const cv::Mat& original_img) {
    cv::Mat img;
    
    // A. Resize: 调整到模型输入大小
    cv::resize(original_img, img, cv::Size(INPUT_WIDTH, INPUT_HEIGHT));

    // B. Color Conversion: BGR -> RGB (OpenCV 默认是 BGR，模型通常要 RGB)
    cv::cvtColor(img, img, cv::COLOR_BGR2RGB);

    // C. Normalize & Convert to Float
    // 先转成 float32 并缩放到 [0, 1]
    img.convertTo(img, CV_32F, 1.0 / 255.0);

    // 准备输入 Tensor 的容器 (大小 = C * H * W)
    std::vector<float> input_tensor;
    input_tensor.resize(INPUT_CHANNELS * INPUT_HEIGHT * INPUT_WIDTH);

    // D. HWC -> CHW (手动搬运内存)
    // OpenCV 的内存布局是: 行 -> 列 -> 通道 (HWC)
    // 我们需要将其拉平成: 通道 -> 行 -> 列 (CHW)
    
    // 指针操作比 .at() 快很多
    float* tensor_data = input_tensor.data(); 

    for (int c = 0; c < INPUT_CHANNELS; ++c) {
        for (int h = 0; h < INPUT_HEIGHT; ++h) {
            for (int w = 0; w < INPUT_WIDTH; ++w) {
                // 获取 OpenCV 图像中的像素值
                // img.ptr<cv::Vec3f>(h)[w] 获取第 h 行第 w 列的像素(包含3个通道)
                float pixel_value = img.ptr<cv::Vec3f>(h)[w][c];

                // 执行减均值除方差 (Normalize)
                pixel_value = (pixel_value - MEAN[c]) / STD[c];

                // 填入 Tensor
                // CHW 索引计算公式: c * (H * W) + h * W + w
                int index = c * (INPUT_HEIGHT * INPUT_WIDTH) + h * INPUT_WIDTH + w;
                tensor_data[index] = pixel_value;
            }
        }
    }

    return input_tensor;
}

// --- 核心：ONNX Runtime 推理引擎类 ---
class OnnxEngine {
private:
    Ort::Env env;
    Ort::Session session;
    Ort::AllocatorWithDefaultOptions allocator;
    
    // 存储输入输出节点的名称
    std::vector<const char*> input_node_names;
    std::vector<const char*> output_node_names;
    
    // 需要持有名称字符串的内存 (ONNX Runtime C++ API 的一个小坑)
    std::vector<std::string> input_node_names_alloc; 
    std::vector<std::string> output_node_names_alloc;

public:
    // 构造函数：加载模型
    OnnxEngine(const std::string& model_path) 
        : env(ORT_LOGGING_LEVEL_WARNING, "TestOnnxEnv"), 
          session(nullptr) { // session 初始化为空
        
        Ort::SessionOptions session_options;
        session_options.SetIntraOpNumThreads(1); // 设置线程数
        
        // 加载模型
        session = Ort::Session(env, model_path.c_str(), session_options);

        // --- 自动获取输入输出节点名称 ---
        // 这一步很重要，因为不同模型的输入节点名字不一样 (比如 "data", "input", "images" 等)
        
        // 1. 获取输入节点信息
        size_t num_input_nodes = session.GetInputCount();
        for(size_t i = 0; i < num_input_nodes; i++) {
            auto input_name = session.GetInputNameAllocated(i, allocator);
            input_node_names_alloc.push_back(input_name.get());
            input_node_names.push_back(input_node_names_alloc.back().c_str());
        }

        // 2. 获取输出节点信息
        size_t num_output_nodes = session.GetOutputCount();
        for(size_t i = 0; i < num_output_nodes; i++) {
            auto output_name = session.GetOutputNameAllocated(i, allocator);
            output_node_names_alloc.push_back(output_name.get());
            output_node_names.push_back(output_node_names_alloc.back().c_str());
        }
        
        std::cout << "Model loaded. Input Name: " << input_node_names[0] << std::endl;
    }

    // 推理函数
    std::vector<float> run_inference(std::vector<float>& input_data) {
        // 1. 定义输入 Tensor 的形状 [Batch, Channel, Height, Width]
        std::vector<int64_t> input_shape = {1, 3, 224, 224};
        
        // 2. 创建内存信息 (CPU)
        Ort::MemoryInfo memory_info = Ort::MemoryInfo::CreateCpu(
            OrtArenaAllocator, OrtMemTypeDefault);

        // 3. 创建输入 Tensor
        // 注意：这里没有发生数据拷贝，只是创建了一个视图指向 input_data
        Ort::Value input_tensor = Ort::Value::CreateTensor<float>(
            memory_info, 
            input_data.data(), 
            input_data.size(), 
            input_shape.data(), 
            input_shape.size()
        );

        // 4. 执行推理 (Run)
        // Run(RunOptions, InputNames, InputValues, InputCount, OutputNames, OutputCount)
        auto output_tensors = session.Run(
            Ort::RunOptions{nullptr}, 
            input_node_names.data(), 
            &input_tensor, 
            1, 
            output_node_names.data(), 
            1
        );

        // 5. 获取输出数据
        // 假设输出是一个 float 数组 (Logits)
        float* floatarr = output_tensors[0].GetTensorMutableData<float>();
        size_t output_size = output_tensors[0].GetTensorTypeAndShapeInfo().GetElementCount();
        
        // 将结果复制到 vector 返回
        return std::vector<float>(floatarr, floatarr + output_size);
    }
};

// --- 3. 后处理: Softmax ---
// 公式: exp(x_i) / sum(exp(x_j))
std::vector<float> softmax(const std::vector<float>& logits) {
    std::vector<float> probabilities(logits.size());
    float sum = 0.0f;
    
    // 为了数值稳定性，通常减去最大值 (防止 exp 溢出)
    float max_val = *std::max_element(logits.begin(), logits.end());

    for (size_t i = 0; i < logits.size(); ++i) {
        probabilities[i] = std::exp(logits[i] - max_val);
        sum += probabilities[i];
    }

    for (size_t i = 0; i < probabilities.size(); ++i) {
        probabilities[i] /= sum;
    }

    return probabilities;
}

int main() {
    std::string image_path = "cat.jpg";
    std::string model_path = "resnet50.onnx";

    // 1. 加载图片
    cv::Mat img = cv::imread(image_path);
    if (img.empty()) {
        std::cerr << "Error reading image." << std::endl;
        return -1;
    }

    // 2. 初始化引擎 (加载模型)
    std::cout << "Loading model..." << std::endl;
    try {
        OnnxEngine engine(model_path);

        // 3. 预处理
        std::vector<float> input_tensor = preprocess(img);

        // 4. 执行真实推理
        std::vector<float> output_logits = engine.run_inference(input_tensor);

        // 5. 后处理
        std::vector<float> probs = softmax(output_logits);

        // 6. 结果展示
        auto max_it = std::max_element(probs.begin(), probs.end());
        int class_id = std::distance(probs.begin(), max_it);
        float confidence = *max_it;

        std::cout << "--------------------------------" << std::endl;
        // ImageNet 中 281-285 都是猫科动物
        std::cout << "Class ID: " << class_id << ", Probability: " << confidence * 100 << "%" << std::endl;
        std::cout << "--------------------------------" << std::endl;

    } catch (const Ort::Exception& e) {
        std::cerr << "ONNX Runtime Error: " << e.what() << std::endl;
    }

    return 0;
}
```



### 代码详解 



#### 1. 预处理 (HWC -> CHW)



这是最“反直觉”的部分。

- 这里的 `preprocess` 函数展示了如何手动解包。
  - `img.ptr<cv::Vec3f>(h)[w][c]`：访问内存中第 $h$ 行，第 $w$ 列的像素，取出第 $c$ 个通道的值。
  - `tensor_data[...] = ...`：将这个值放入我们准备好的一维数组（`std::vector`）的对应位置。由于 vector 本质是一段连续内存，我们可以把它直接传给 C++ 的推理引擎。



#### 2. 归一化 (Normalize)



公式通常是：



> $$Pixel_{norm} = \frac{Pixel / 255.0 - Mean}{Std}$$



这确保了输入数据分布在 0 附近，这是神经网络训练时的假设。



#### 3. Softmax (数学部分)



Softmax 将模型的原始输出（Logits，可以是负数或很大的数）转换成概率分布（所有值加起来为 1）。



> $$P(y_i) = \frac{e^{x_i}}{\sum_{j} e^{x_j}}$$



代码中加入了 max_val 的处理，这在工程上是为了防止 $e^{100}$ 这种数导致浮点溢出（NaN）。

------



### 常见坑点与调试技巧



1. **颜色通道搞反 (BGR vs RGB):**
   - OpenCV `imread` 读出来默认是 **BGR**。
   - 几乎所有训练好的模型（PyTorch/TensorFlow）都预期 **RGB**。
   - **症状**: 预测结果很离谱，但不是报错。
   - **解决**: 务必使用 `cv::cvtColor(img, img, cv::COLOR_BGR2RGB);`。
2. **数据类型错误:**
   - OpenCV 默认是 `uint8` (0-255)。模型输入通常需要 `float32`。
   - **解决**: 使用 `img.convertTo(img, CV_32F)`。
3. **内存越界:**
   - 在 HWC 转 CHW 的三层循环中，如果索引计算错一点（比如 `w` 和 `h` 搞反），程序会直接 Crash (Segmentation Fault)。



然后，最后给出CMakeLists.txt:

```cmak
cmake_minimum_required(VERSION 3.10)
project(RealInference)

set(CMAKE_CXX_STANDARD 17) # ORT 建议用 C++17 或更高

find_package(OpenCV REQUIRED)

# --- 配置 ONNX Runtime ---
# 这里的路径对应你解压的文件夹名
set(ORT_ROOT "${CMAKE_SOURCE_DIR}/onnxruntime") 

# 包含头文件
include_directories(${ORT_ROOT}/include)

# 链接动态库
link_directories(${ORT_ROOT}/lib)

add_executable(RealInference main.cpp)

# 链接 OpenCV 和 ONNX Runtime
# 注意: libonnxruntime.so 的具体名字可能是 libonnxruntime.so.1.16.3，根据实际情况，
# 或者通常链接 libonnxruntime.so 即可
target_link_libraries(RealInference ${OpenCV_LIBS} onnxruntime)
```



