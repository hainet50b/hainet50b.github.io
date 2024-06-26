# telnetコマンドでHTTPリクエストを送信
{:.no_toc}

* toc
{:toc}

telnetでもHTTPの仕様に従ったHTTPリクエストを送信することでHTTPレスポンスを確認できる。

```
telnet example.com 80

Trying 93.184.215.14...
Connected to example.com.
Escape character is '^]'.

GET / HTTP/1.1
Host: example.com

HTTP/1.1 200 OK
Age: 588810
Cache-Control: max-age=604800
Content-Type: text/html; charset=UTF-8
Date: Thu, 09 May 2024 14:15:17 GMT
Etag: "3147526947+ident"
Expires: Thu, 16 May 2024 14:15:17 GMT
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Server: ECAcc (sac/2536)
Vary: Accept-Encoding
X-Cache: HIT
Content-Length: 1256

<!doctype html>
<html>
<head>
    <title>Example Domain</title>

    <meta charset="utf-8" />
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <style type="text/css">
    body {
        background-color: #f0f0f2;
        margin: 0;
        padding: 0;
        font-family: -apple-system, system-ui, BlinkMacSystemFont, "Segoe UI", "Open Sans", "Helvetica Neue", Helvetica, Arial, sans-serif;

    }
    div {
        width: 600px;
        margin: 5em auto;
        padding: 2em;
        background-color: #fdfdff;
        border-radius: 0.5em;
        box-shadow: 2px 3px 7px 2px rgba(0,0,0,0.02);
    }
    a:link, a:visited {
        color: #38488f;
        text-decoration: none;
    }
    @media (max-width: 700px) {
        div {
            margin: 0 auto;
            width: auto;
        }
    }
    </style>
</head>

<body>
<div>
    <h1>Example Domain</h1>
    <p>This domain is for use in illustrative examples in documents. You may use this
    domain in literature without prior coordination or asking for permission.</p>
    <p><a href="https://www.iana.org/domains/example">More information...</a></p>
</div>
</body>
</html>
```