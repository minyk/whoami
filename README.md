# whoami

[![Docker Pulls](https://img.shields.io/docker/pulls/containous/whoami.svg)](https://hub.docker.com/r/containous/whoami/)

Tiny Go webserver that prints os information and HTTP request to output

```console
$ docker run -d -P --name iamfoo containous/whoami
$ docker inspect --format '{{ .NetworkSettings.Ports }}'  iamfoo
map[80/tcp:[{0.0.0.0 32769}]]
$ curl "http://0.0.0.0:32769"
Hostname :  6e0030e67d6a
IP :  127.0.0.1
IP :  ::1
IP :  172.17.0.27
IP :  fe80::42:acff:fe11:1b
GET / HTTP/1.1
Host: 0.0.0.0:32769
User-Agent: curl/7.35.0
Accept: */*
```

# API

### /
Response with simple status page.
```
Hostname: app
IP: 127.0.0.1
IP: ::1
IP: 172.18.0.4
IP: fe80::42:acff:fe12:4
GET / HTTP/1.1
Host: sit.filedownload.rentacar.ajway.co.kr
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9,ko;q=0.8
Connection: close
Cookie: _ga=GA1.3.1339917694.1540942768; _BS_GUUID=f5vtNQ1LfAWTU4iKDllK7mhHBz3xKel0kQt7HvTW; _wp_uid=1-88084a026482d482d24cd7e5cad7cf6c-s1540942768.5069|mac_osx|chrome-1nv1vw4; _TRK_AUIDA_13796=46e1d0d1eab5f48d910cf6c27f0920b9:3; _TRK_AUIDA_13798=8762cbea226713092ce6ab55f82e90a3:2; _TRK_AUIDA_13797=483de35e32a70b5f7aaae8e8751d0d0d:3
Upgrade-Insecure-Requests: 1
X-Forwarded-For: 192.10.100.94
X-Forwarded-Proto: https
```

* Available Query Parameter
 * `wait`: Sleep this duration before send a response. The value is parsed with `time.ParseDuration`. See this: https://golang.org/pkg/time/#ParseDuration. Example:
```
http://localhost/?wait=10s
```

### /api
Response the same status page, but in JSON format.
```json
{
  "hostname":"app",
  "ip":["127.0.0.1","172.19.0.3"],
  "headers":{
    "Accept":["text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8"],
    "Accept-Encoding":["gzip, deflate, br"],
    "Accept-Language":["en-US,en;q=0.9,ko;q=0.8"],
    "Connection":["close"],
    "Cookie":["_ga=GA1.3.1339917694.1540942768; _BS_GUUID=f5vtNQ1LfAWTU4iKDllK7mhHBz3xKel0kQt7HvTW; _wp_uid=1-88084a026482d482d24cd7e5cad7cf6c-s1540942768.5069|mac_osx|chrome-1nv1vw4; _TRK_AUIDA_13796=46e1d0d1eab5f48d910cf6c27f0920b9:3; _TRK_AUIDA_13798=8762cbea226713092ce6ab55f82e90a3:2; _TRK_AUIDA_13797=483de35e32a70b5f7aaae8e8751d0d0d:3"],
    "Upgrade-Insecure-Requests":["1"],
    "User-Agent":[
      "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36"
    ],
    "X-Forwarded-For":["192.10.100.94"],
    "X-Forwarded-Proto":["https"]
  }
}
```

### /bench
Return `text/plain` string "1" with below header:
```
Connection: keep-alive
Content-Type: text/plain
```

### /data
Return random data in the response.

* Available Query Parameter
 * `size`: Size of the data in the response. Default size is 1.
 * `unit`: Unit of the data. One of the `kb`, `mb`, `gb`, `tb`. If not defined, the unit is byte.
 * `attachment`: If `true`, the `data.txt` file contains the data. Default is `false`.

### /echo
Echo input data to response using `websocket`. Client should handle `websocket` handshake.

### /health
Return HTTP status code

* `GET` method: Return `200 OK`
* `POST` method: Update health check status code with the request's body.
```
curl -XPOST https://app/health -d'201'
```
 * In the server's log:
 ```
 Update health check status code [201]
 ```
