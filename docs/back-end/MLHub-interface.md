# ML-Hub Interface

## 概述

所有请求前带一个默认前缀 `/api` 用于nginx重定向到ML-Hub运行的端口。

## 接口

### 测试

#### Hello

URI: `/`

Method: `GET` `POST`

请求参数：

| 参数名 | 参数类型 | 描述 |
| ------ | -------- | ---- |

返回参数：

纯文本 Hello

#### Hello name

URI: `/hello/<name>`

Method: `GET` `POST`

请求参数：

| 参数名 | 参数类型 | 描述          |
| ------ | -------- | ------------- |
| name   | string   | 在URI里面请求 |

返回参数：

纯文本 Hello \<name\>

### 查询

(未测试)

#### 查询本地存储的图片

URI: `/images/<image_name>`

Method:  `POST`

请求参数：

| 参数名     | 参数类型 | 描述          |
| ---------- | -------- | ------------- |
| image_name | string   | 在URI里面请求 |

返回参数：

如果存储中有图片，返回图片。

否则返回

```json
		{
            "code": 404,
            "message": "image not found"
        }
```

### 上传

#### 上传图片

MLHub接收图片并保存到本地

URI: `/upload`

Method:  `POST` `OPTIONS`

请求参数：

| 参数名 | 参数类型               | 描述 |
| ------ | ---------------------- | ---- |
| image  | image/png or image/jpg | 图像 |

返回参数：

		{
	        "code": 200,
	        "message": urls
	    }

urls是图片的远程地址。

### 机器学习

#### 指派任务

URI: `/ml/dispatch`

Method:  `POST`

请求参数 (JSON)：

| 参数名      | 参数类型 | 描述                   |
| ----------- | -------- | ---------------------- |
| model       | string   | 模型名称               |
| local_image | string   | 已上传到图片池的图片名 |

模型返回的json，例如 *轴承缺陷检测模型ConvNeXt_tiny*

```json
{
	"result": 0,
	"verbose": "broken"
}
```

`result`为模型原始输出，`verbose`是便于人类阅读的对`result`的描述。（0为broken，1为ok）

不同的模型返回参数不同。

找不到模型

```json
{
    'code': 404,
	'message': 'image not found'
}
```



#### 指派任务（已弃用）

不建议使用。测试时候可以使用。

URI: `/ml/dispatch/<model>/<data>`

Method:  `POST`

请求参数：

| 参数名 | 参数类型 | 描述     |
| ------ | -------- | -------- |
| model  | string   | 模型名称 |
| data   | string   | 图片名称 |

返回参数：

特别的，`model==‘TESTMODEL’`会直接返回

```json
{
	'code': 200,
	'message': 'Test success!'
}
```

找不到模型

```json
{
    'code': 404,
	'message': 'image not found'
}
```

