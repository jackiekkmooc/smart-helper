[TOC]

# 智能助手简介

智能助手是一款大型语言模型，拥有强大的语义理解能力、丰富的专业领域知识、强大的逻辑推理能力，能够进行自然语言理解、对话、问答等任务。

# 接口说明

传输协议: `HTTP`

编解码: `JSON`

请求方式: `POST`

请求URI: `/smart-helper/chat`

会话说明: `同个chat_id最大并发数为1，且会话内的记录条数最大为200(问+答 <= 200),超过200条系统会自动清理历史对话`


# 协议说明

## 请求体

| 参数名称        | 必填  | 类型        | 描述                 |
|-------------|-----|-----------|--------------------|
| chat_id     | 是   | string    | 会话ID,同个会话下的问答具备上下文 |
| content     | 是   | string    | 聊天内容               |
| signature   | 是   | Signature | 签名信息               |

### Signature

| 参数名称      | 必填  | 类型     | 描述                                           |
|-----------|-----|--------|----------------------------------------------|
| secret_id | 是   | string | 密钥ID, secret_id和secret_key需要提前分配             |
| sign      | 是   | string | 签名, sign=md5(secret_id+secret_key+timestamp) |
| timestamp | 是   | int64  | unix时间戳,单位秒                                  |

### sign计算方式示例:
```
# shell
secret_id=123
secret_key=abc
timestamp=1672502400
sign=$(echo -n "$secret_id$secret_key$timestamp" | md5sum  | awk '{print $1}')
echo "sign=$sign"
```

## 返回体

| 参数名称          | 类型     | 描述             |
|---------------|--------|----------------|
| code          | int    | 错误码,见下方说明      |
| msg           | string | 错误信息           |
| trace_id      | string | 追踪ID,用于服务端问题定位 |
| reply_content | string | 回复内容           |

### 错误码 `code`说明
| 错误码 | 说明     | 
|-----|--------|
| 0   | 成功     |
| 1   | 非法参数   |
| 2   | 鉴权不通过  |
| 3   | 服务内部错误 |
| 999 | 未知错误   |

# 请求示例

## 请求体

```
{
    "chat_id": 1,
    "content": "你好",
    "signature": {
        "secret_id": "123",
        "sign": "447876e87285b6904e9040d98a86482b",
        "timestamp": 1672502400
    }
}
```

## 返回体

```
{
    "code": 0,
    "msg": "success",
    "trace_id": "2YT9BNgu7mgxqidShWTloYQZ1qQ",
    "reply_content": "你好！很高兴见到你。请问有什么我可以帮助你的？"
}
```
