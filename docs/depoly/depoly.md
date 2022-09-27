# 部署本项目

## 部署后端

后端仓库地址：

https://github.com/NUIST-DEV/back-end

### 概述

后端由两个部分组成。分别是管理网络接口以及非机器学习事物的`MLHub`，以及管理模型的`ModelHub`。

`ModelHub`是一个类，会被`MLHub`启动时载入并初始化。ModelHub在初始化时将会自动载入并实例化`models`文件夹下所有的模型。

在开发中，你只需要启动项目根目录下的`dev.ipynb`，就能启动`MLHub`。如果一切正常，并且提示Flask正在运行，那么恭喜你，后端已经在正常运行了，不需要任何的配置。

### 部署

1. 克隆本仓库

```bash
git clone git@github.com:NUIST-DEV/back-end.git
```

2. 下载缺失的模型权重文件，并放入对应的文件夹里面。

例如`back-end/ModelHub/models/ConvNeXt/`中应该放入`best_model.pth`。

如果你只想做测试，可以直接删掉其他的模型文件夹，只留下想要的。但是至少有一个模型。

ModelHub会自动载入这些模型。

3. 安装python环境。
   你可以参考(群里的)`requirements.txt`来和我安装一样的包。

也可以自己手动安装必备的包。这里列举一下。

我的Python版本：3.9.8

- Torch (GPU版更好)
  - PyTorch TorchVision TorchAudio 一套
- NumPy
- Flask
- cv2
- 其他杂七杂八的包，缺了自己装即可。

手动安装Torch的whl文件，在此处下载https://download.pytorch.org/whl/torch_stable.html

4. 运行`dev.ipynb`

## 部署前端

项目地址：

https://github.com/NUIST-DEV/front-end

### 概述

前端是Vue，npm包管理。

### 部署

0. 确保电脑上有node.js环境，如果没有自行安装高版本的node.js （我的是v16.17.0）

1. 克隆本仓库

```bash
git clone git@github.com:NUIST-DEV/front-end.git
```

2. `cd front-end` 进入文件夹
3. `npm install`  安装缺失的包
4. 如果缺失`vue-cli`，全局安装`vue-cli`
5. 运行`npm run serve`进行带热重载的开发。

### 部署Nginx

后端默认运行在7001端口，调试前端运行在3000端口。导致请求会有问题。

前端已经开启了URI重写，任何以`/api`开头的请求会被重写到`localhost`（不带端口，默认80）

Nginx会监听80端口，识别`/api`开头的请求，并跨域转发到7001端口。

Nginx配置：

```nginx
worker_processes  1;
events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }


        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        location /api {
            add_header 'Access-Control-Allow-Origin' '*';
            add_header 'Access-Control-Allow-Credentials' 'true';
            add_header 'Access-Control-Allow-Methods' 'GET, PUT, POST, DELETE, OPTIONS';
            add_header 'Access-Control-Allow-Headers' '*';
            proxy_pass   http://127.0.0.1:7001;
        }

        
        # location /m {
        #   add_header 'Access-Control-Allow-Origin' '*';
        #   add_header 'Access-Control-Allow-Credentials' 'true';
        #   add_header 'Access-Control-Allow-Methods' 'GET, PUT, POST, DELETE, OPTIONS';
        #   add_header 'Access-Control-Allow-Headers' '*';
        #   proxy_pass   http://127.0.0.1:7002;
        # }
    }
}
```

