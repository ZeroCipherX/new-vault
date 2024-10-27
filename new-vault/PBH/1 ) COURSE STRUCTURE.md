---
title: '==**COURSE STRUCTURE**=='
updated: 2024-05-25 13:43:57Z
created: 2024-05-23 21:52:15Z
---

==**COURSE STRUCTURE**==

![Screenshot from 2024-05-23 17-49-15.png](../Screenshot%20from%202024-05-23%2017-49-15.png)

## **IMPORTANCE OF WEB APP SECURITY**

![Screenshot from 2024-05-24 02-02-10.png](../Screenshot%20from%202024-05-24%2002-02-10.png)

## Web Application Security Standards and Best Practices

![Screenshot from 2024-05-24 02-26-13.png](../Screenshot%20from%202024-05-24%2002-26-13.png)

## **WEB SECURITY STANDARDS**

**1 OWASP TOP 10**

**2 COMMON WEAKNESS ENUMERATION**

**3 SANS TOP 25**

<ins>https://owasp.org/Top10/</ins>

<ins>https://cwe.mitre.org/index.html</ins>

<ins>https://www.sans.org/top25-software-errors/</ins>

&nbsp;

## Bug Bounty Hunting vs Penetration Testing

&nbsp;

![Screenshot from 2024-05-24 02-51-41.png](../Screenshot%20from%202024-05-24%2002-51-41.png)

![Screenshot from 2024-05-24 02-54-10.png](../Screenshot%20from%202024-05-24%2002-54-10.png)

# **Phases of a Web Application Penetration Test**

**![Screenshot from 2024-05-24 03-06-25.png](../Screenshot%20from%202024-05-24%2003-06-25.png)**

# Understanding Scope, Ethics, Code of Conduct, etc.

![Screenshot from 2024-05-24 03-40-27.png](../Screenshot%20from%202024-05-24%2003-40-27.png)![Screenshot from 2024-05-24 03-40-33.png](../Screenshot%20from%202024-05-24%2003-40-33.png)![Screenshot from 2024-05-24 03-46-08.png](../Screenshot%20from%202024-05-24%2003-46-08.png)![Screenshot from 2024-05-24 03-48-16.png](../Screenshot%20from%202024-05-24%2003-48-16.png)![Screenshot from 2024-05-24 03-49-42.png](../Screenshot%20from%202024-05-24%2003-49-42.png)![Screenshot from 2024-05-24 03-51-37.png](../Screenshot%20from%202024-05-24%2003-51-37.png)![Screenshot from 2024-05-24 03-52-22.png](../Screenshot%20from%202024-05-24%2003-52-22.png)![Screenshot from 2024-05-24 03-54-12.png](../Screenshot%20from%202024-05-24%2003-54-12.png)![Screenshot from 2024-05-24 03-56-40.png](../Screenshot%20from%202024-05-24%2003-56-40.png)![Screenshot from 2024-05-24 03-57-23.png](../Screenshot%20from%202024-05-24%2003-57-23.png)

## **Common Scoping Mistakes**

![Screenshot from 2024-05-24 15-05-02.png](../Screenshot%20from%202024-05-24%2015-05-02.png)

![Screenshot from 2024-05-24 15-09-25.png](../Screenshot%20from%202024-05-24%2015-09-25.png)

![Screenshot from 2024-05-24 15-27-26.png](../Screenshot%20from%202024-05-24%2015-27-26.png)

![Screenshot from 2024-05-25 02-54-06.png](../Screenshot%20from%202024-05-25%2002-54-06.png)

## **LAB SETUP**

[bugbounty-v1.1.zip](../bugbounty-v1.1.zip)

Lab setup in Kali Linux  
Step 1: Installing required packages  
sudo apt update

sudo apt upgrade

sudo apt install docker.io

sudo apt install docker-compose

Restart your Kali VM.

Step 2: Unpack the labs  
Copy the labs to a directory on your system (e.g. /home/kali/labs)

cd /home/kali/labs

unzip bugbounty-v1.0.zip

cd bugbounty

sudo docker-compose up

The first time you run this it will take some time because it needs to download the docker images to your machine. Next time you run it, it should only take 5-30 seconds.

Step 3: Setup permissions  
In a different terminal, navigate to where you unzipped the lab (e.g. /home/kali/labs/bugbounty) and run the set-permissions.sh script. This is used for labs that require write access, such as the file upload attacks.

./set-permissions.sh

Browse to http://localhost

The first time you load the lab the database will need to be initialized, just follow the instructions in the red box by clicking the link, then coming back to the homepage.

Enjoy your labs!