---
title: 好用的Python异步请求库httpx
date: 2024-07-08 17:56:52
tags:
---
## 引言

HTTP请求库是许多Python开发者在编写网络应用程序时常用的工具。无论是进行网页抓取、数据传输、还是与API进行交互，HTTP请求库都显得尤为重要。传统的requests库已经广为人知并被广泛应用，但它没有提供对异步的支持。本文将介绍一款强大的异步HTTP请求库——httpx，并通过具体的示例展示其从基础到进阶的使用方法。

## httpx简介

httpx是一个现代的、功能丰富的Python HTTP客户端，不仅支持同步API，还支持异步API，可以处理HTTP/1.1和HTTP/2请求。它的API设计与requests库高度一致，所以如果你曾使用过requests库，将非常容易上手httpx。要安装httpx库，只需运行以下命令：

```bash
pip install httpx
```

httpx的主要特性包括：
1. 支持同步和异步请求
2. 完整的HTTP/1.1和HTTP/2支持
3. 支持Cookie持久化、重定向和身份验证等高级功能
4. 与requests库兼容的API设计

## 基础使用示例

下面我们通过一些简单的示例来看一下httpx库的基本使用方法。

### 同步请求
以下是一个发送GET请求的示例：

```python
import httpx

response = httpx.get('https://httpbin.org/get')
print(response.status_code)
print(response.json())
```
上述代码将GET请求发送到`https://httpbin.org/get`，并打印响应的状态码和JSON格式的响应内容。

同样的，我们也可以发送POST请求：

```python
response = httpx.post('https://httpbin.org/post', data={'key': 'value'})
print(response.status_code)
print(response.json())
```

在这个示例中，POST请求将一个键值对发送到指定URL，并打印响应。

### 响应处理
httpx的响应对象提供了多种方法来处理服务器的响应。例如，可以通过`response.text`获取响应的纯文本内容，通过`response.content`获取字节内容，或者通过`response.json()`直接解析JSON格式的响应。

## 进阶使用示例

在使用httpx进行更复杂的操作时，我们推荐使用客户端对象（Client），它可以在多个请求之间保持连接，并提供更高级的配置选项。

### 使用客户端对象
以下示例展示了如何使用httpx的客户端对象来管理连接。

```python
import httpx

with httpx.Client() as client:
    response = client.get('https://httpbin.org/get')
    print(response.json())
```

在上述代码中，客户端对象通过上下文管理器创建，确保在操作结束时正确地释放资源。

### 处理Cookie
客户端对象可以自动管理Cookie。

```python
import httpx

with httpx.Client() as client:
    client.get('https://httpbin.org/cookies/set?name=value')
    response = client.get('https://httpbin.org/cookies')
    print(response.json())
```

### 重定向
默认情况下，httpx会自动处理重定向，可以通过参数禁用。

```python
response = httpx.get('https://httpbin.org/redirect/1', follow_redirects=True)
print(response.status_code)
print(response.url)
```

### 超时和身份验证
你可以设置超时和身份验证选项来增强安全性和可靠性。

```python
from httpx import DigestAuth

response = httpx.get(
    'https://httpbin.org/digest-auth/auth/user/pass',
    auth=DigestAuth('user', 'pass'),
    timeout=5.0
)
print(response.status_code)
```

## 异步请求

异步编程的优势在于能够在等待I/O操作（如网络请求）时执行其他任务，从而提高应用程序的效率。httpx库对Python的asyncio框架提供了良好的支持，使得异步HTTP请求变得非常简单。

### 异步GET请求
以下是一个发送异步GET请求的示例：

```python
import httpx
import asyncio

async def fetch():
    async with httpx.AsyncClient() as client:
        response = await client.get('https://httpbin.org/get')
        print(response.json())

asyncio.run(fetch())
```

在这个示例中，`httpx.AsyncClient`对象被用于发送异步请求，而`await`关键字则用于等待请求的响应。

### 并发请求
通过asyncio，您可以轻松地并发地发送多个HTTP请求。

```python
import httpx
import asyncio

async def fetch(url):
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        print(response.json())

async def main():
    urls = ['https://httpbin.org/get'] * 5
    tasks = [fetch(url) for url in urls]
    responses = await asyncio.gather(*tasks)
	# 统一处理响应结果
	for resp in responses:
		print(resp)

asyncio.run(main())
```

在这个示例中，`asyncio.gather`函数用于并发执行多个请求任务，每个任务都是对相同URL的GET请求。

### 处理超时和重试
在异步环境中处理超时和重试同样非常重要。

```python
import httpx
import asyncio

async def fetch_with_timeout(url):
    async with httpx.AsyncClient(timeout=10.0) as client:
        response = await client.get(url)
        return response

async def main():
    try:
        response = await fetch_with_timeout('https://httpbin.org/delay/5')
        print(response.json())
    except httpx.RequestError as exc:
        print(f"An error occurred: {exc}")

asyncio.run(main())
```

## 结论

httpx库凭借其强大的功能和灵活的API设计，为Python开发者提供了一种高效进行HTTP请求的方式。它不仅在设计风格上参考了requests库易于使用的特点，还在此基础上增加了对异步编程、HTTP/2、Cookie管理、重定向处理和自定义身份验证等高级特性的支持。

在需要处理大量HTTP请求，并希望提高代码执行效率和响应速度的场景中，httpx是一个理想的选择。不论是构建爬虫、集成第三方服务API，还是从事网络测试和监控，httpx都能助你一臂之力。


