---
title: Gunicorn部署pyzbar程序
date: 2025-11-28 08:20:21
tags:
- AI Gunicorn Uvicorn Python 
categories:
- Python 
---



这是一个非常典型的后端微服务需求。为了满足**可扩展性**（后续增加OCR、图像处理等功能）和**内网部署**的需求，我建议使用 **FastAPI** 作为 Web 框架（因为它性能高、开发快、原生支持异步），配合 **Gunicorn** 作为 WSGI/ASGI 服务器进行部署。



最近组里的小伙伴，搞条形码识别，没搞定，我简单写了个识别的程序，然后用Gunicorn 部署了下，很有参考意义。为了满足**可扩展性**（后续增加OCR、图像处理等功能）和**内网部署**的需求，使用 **FastAPI** 作为 Web 框架（因为它性能高、开发快、原生支持异步），配合 **Gunicorn** 作为 WSGI/ASGI 服务器进行部署。

整个小项目遵循了**分层架构**（路由层、业务层、工具层），这样以后要加新功能时，就不需要改动原有的代码。

以下是完整的解决方案，包含代码结构、部署策略和调用方式。

------



### 1. 项目结构设计



为了应对未来的需求变更，我们将代码分为以下几层：

Plaintext

```
barcode_service/
├── main.py             # 入口文件，负责启动 App
├── gunicorn_conf.py    # Gunicorn 配置文件
├── requirements.txt    # 依赖包
└── app/
    ├── __init__.py
    ├── api.py          # 路由层：定义 URL 和参数
    └── services.py     # 业务层：具体的识别逻辑（封装你的代码）
```

------



### 2. 完整代码实现





#### Step 1: 准备依赖 (requirements.txt)



我们需要 FastAPI, Uvicorn (配合 Gunicorn 使用), 和 python-multipart (用于文件上传)。

Plaintext

```
fastapi
uvicorn
gunicorn
pyzbar
pillow
python-multipart
```



#### Step 2: 业务逻辑层 (app/services.py)



这里封装核心代码。重点改进是：**不再读取本地路径，而是直接读取内存中的文件流**。这样调用方只需要上传图片，不需要关心服务器的文件系统。

Python

```
# app/services.py
from pyzbar.pyzbar import decode
from PIL import Image
import io

class ScannerService:
    @staticmethod
    def scan_barcode_from_bytes(image_bytes: bytes):
        try:
            # 直接从内存字节流读取图片，避免磁盘IO
            img = Image.open(io.BytesIO(image_bytes))
            
            # 解决部分图片旋转问题（可选，视具体场景）
            # img = ImageOps.exif_transpose(img) 

            decoded_objects = decode(img)
            results = []

            for obj in decoded_objects:
                code_data = obj.data.decode('utf-8')
                code_type = obj.type
                rect = {
                    "left": obj.rect.left,
                    "top": obj.rect.top,
                    "width": obj.rect.width,
                    "height": obj.rect.height
                }
                
                results.append({
                    "type": code_type,
                    "content": code_data,
                    "location": rect
                })
            
            return results
        except Exception as e:
            print(f"Error processing image: {e}")
            return []

# 后续可以在这里添加 scan_ocr, scan_face 等新类或方法
```



#### Step 3: 路由接口层 (app/api.py)



这里定义对外的接口。

Python

```
# app/api.py
from fastapi import APIRouter, UploadFile, File, HTTPException
from app.services import ScannerService

router = APIRouter()

@router.post("/scan/barcode")
async def extract_barcode(file: UploadFile = File(...)):
    """
    接收上传的图片文件，返回识别到的条码信息
    """
    # 简单的格式校验
    if file.content_type not in ["image/jpeg", "image/png", "image/bmp"]:
        raise HTTPException(status_code=400, detail="Invalid image format")

    content = await file.read()
    
    # 调用业务层
    results = ScannerService.scan_barcode_from_bytes(content)
    
    return {
        "filename": file.filename,
        "count": len(results),
        "data": results
    }

# 可以在这里添加 @router.post("/scan/ocr") 等
```



#### Step 4: 应用入口 (main.py)



Python

```
# main.py
from fastapi import FastAPI
from app.api import router

app = FastAPI(
    title="Intranet Image Service",
    description="内网图像识别微服务",
    version="1.0.0"
)

# 注册路由
app.include_router(router, prefix="/api/v1")

@app.get("/health")
def health_check():
    return {"status": "ok"}
```



#### Step 5: Gunicorn 配置 (gunicorn_conf.py)



这是生产环境部署的关键。

Python

```
# gunicorn_conf.py
import multiprocessing

# 监听内网所有IP的 8000 端口
bind = "0.0.0.0:8000"

# 工作模式：FastAPI 需要使用 uvicorn 的 worker
worker_class = "uvicorn.workers.UvicornWorker"

# 进程数：通常是 CPU 核数 * 2 + 1
workers = multiprocessing.cpu_count() * 2 + 1

# 超时时间：图像处理可能比较慢，设置长一点（例如 60秒）
timeout = 60

# 日志配置
loglevel = "info"
accesslog = "-"  # 输出到标准输出
errorlog = "-"
```

------



### 3. 部署策略



由于涉及到 `pyzbar`，它底层依赖系统的 `zbar` 共享库，这是部署中最容易报错的地方。



#### 第一步：安装系统级依赖 (Linux环境)



我的服务器是 Centos：

Bash

```
# 1. 安装 EPEL 仓库 (Extra Packages for Enterprise Linux)
sudo yum install epel-release -y

# 2. 更新 yum 缓存
sudo yum makecache

# 3. 安装 zbar 和相关开发库 (以及 Python3 和 pip，如果没有的话)
# 注意：FastAPI 推荐 Python 3.8+。如果你的 CentOS 是 7，默认 Python 可能会比较老，需注意版本。
sudo yum install zbar zbar-devel python3 python3-devel -y

# 4. 安装 gcc (编译某些 python 库可能需要)
sudo yum install gcc -y
```

这里第一步一般搞不定，所以需要科学的办法：
```
cd /etc/yum.repos.d/
```

做好备份，这是CentOS-Base.repo：

````
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the 
# remarked out baseurl= line instead.
#
#
 
[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/os/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#released updates 
[updates]
name=CentOS-$releasever - Updates - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/updates/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/extras/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
 
#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/contrib/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/contrib/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7


````

然后是：epel.repo

```
[epel]
name=Extra Packages for Enterprise Linux 7 - $basearch
baseurl=http://mirrors.aliyun.com/epel/7/$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
 
[epel-debuginfo]
name=Extra Packages for Enterprise Linux 7 - $basearch - Debug
baseurl=http://mirrors.aliyun.com/epel/7/$basearch/debug
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=0
 
[epel-source]
name=Extra Packages for Enterprise Linux 7 - $basearch - Source
baseurl=http://mirrors.aliyun.com/epel/7/SRPMS
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=0

```





#### 第二步：安装 Python 环境



Bash

```
# 创建虚拟环境
python3 -m venv venv
source venv/bin/activate

# 安装依赖
pip install -r requirements.txt
```



#### 第三步：使用 Systemd 托管 (推荐)



为了保证服务挂掉自动重启，以及开机自启，建议创建一个 systemd 服务文件。

创建文件 `/etc/systemd/system/barcode-service.service`：

Ini, TOML

```
[Unit]
Description=Gunicorn instance to serve Barcode Service
After=network.target

[Service]
# 修改为你的实际用户和路径
User=root
Group=root
WorkingDirectory=/path/to/your/barcode_service
Environment="PATH=/path/to/your/barcode_service/venv/bin"
# 启动命令
ExecStart=/path/to/your/barcode_service/venv/bin/gunicorn -c gunicorn_conf.py main:app

[Install]
WantedBy=multi-user.target
```

**启动服务：**

Bash

```
sudo systemctl daemon-reload
sudo systemctl start barcode-service
sudo systemctl enable barcode-service
```

------



### 4. 外部如何调用 (客户端示例)



现在服务运行在服务器的 8000 端口，接口地址是 `http://<服务器IP>:8000/api/v1/scan/barcode`。



#### 方式 A: 使用 Python requests (比如其他后端服务调用)



Python

```
import requests

url = "http://192.168.1.100:8000/api/v1/scan/barcode"
file_path = "product_photo.jpg"

with open(file_path, "rb") as f:
    # 这里的 'file' 对应 FastAPI 中的参数名
    files = {"file": ("filename.jpg", f, "image/jpeg")}
    response = requests.post(url, files=files)

print(response.json())
```



#### 方式 B: 使用 cURL (测试用)



Bash

```
curl -X POST "http://192.168.1.100:8000/api/v1/scan/barcode" \
     -H "accept: application/json" \
     -H "Content-Type: multipart/form-data" \
     -F "file=@./product_photo.jpg"
```

------



### 5. 关于“后续的需求更新”策略



这套架构如何应对未来的变更：

1. **新增识别类型 (如 OCR):**
   - 在 `app/services.py` 增加一个 `OCRService` 类。
   - 在 `app/api.py` 增加一个 `@router.post("/scan/ocr")` 接口。
   - 不需要修改现有的条码识别代码，互不影响。
2. **性能瓶颈:**
   - 如果识别太慢，Gunicorn 的 `workers` 数量可以调整。
   - 因为是 HTTP 接口，如果未来压力巨大，可以将 `api.py` 里的逻辑改为“接收图片 -> 丢入 Redis 队列”，然后另写一个 Worker 脚本消费队列。
3. **内网变公网:**
   - 目前的 Gunicorn 配置在 Nginx 后面加一层反向代理即可，无需改动代码。

