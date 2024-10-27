---
title: '==**WEB APP TECHNOLOGIES**=='
updated: 2024-05-25 13:42:05Z
created: 2024-05-25 09:47:34Z
---

# ==**WEB APP TECHNOLOGIES**==

**HTML** or hypertext markup language is the standard markup language used to create web pages It structure the content such as test images and links and it consists of element represented by tags which define the structure and content of pages and generally acts like a skeleton of a web page.

![Screenshot from 2024-05-25 07-44-44.png](../Screenshot%20from%202024-05-25%2007-44-44.png)

Html injection as an indication of potential XSS  
Next we have cascading style sheets and a style sheet is used for describing the representation of a document written in html It consists of rule set where the rule define the styling for html elements and acts like the skin and aesthetic of a web page making it visually

css use at some rare occasion like stealing user inputs and if we are able to inject css

JavaScript High level interpreted programming language primarily used to make web pages interactive It makes use of variables, functions, objects and events and act as the behavior of a web page adding interactivity and dynamic functionality IT can gives us interesting insights in behavior on the front end as well as information about endpoints the application is using.

Node.js is also gaining hype learning bit of js will double up when it comes to server-side attacks against node applications

**Server side -> we are referring to operations that are performed on a web server where the application is hosted This often include task like data storage, retrieval and processing**

**Client side -> we are referring to operations that are performed in the browser and on the users device It deals with presenting data to the user and handling user interactions**

## **Traditional VS Modern Web App  


- **Traditional web app generally have a full page reload for every request The server return complete html pages and plays a larger role in generating page contents.**
- **Modern Applications are now loading data in the background and using frameworks like react or foo to create single page applications generally they offer a more seamless user experience by updating portions of a page with out requiring a full page reload**

# ==**HTTP & DNS**==

HTTP is a request response protocol A client usually a web browser sends a request  to a server and then that responds to that request. When a request is made we have to choose a HTTP method and some of the common methods that seen often are GET, which is used to fetch a resource, for example a web page or an image. POST which is used to submit data to the server  so when are posting forms for example PUT to update a resource so e.g we are updating our account email address and DELETE to remove a resource from the server

**RESPONSE CODES  
![Screenshot from 2024-05-25 09-17-44.png](../Screenshot%20from%202024-05-25%2009-17-44.png)
Remember: HTTP** is stateless, meaning that each request from the client to the server is treated as a new standalone request, with no memory of prior request. This is why mechanism like cookies are used to maintain state across multiple requests Usually a unique cookie is given to a user once they are logged in and then the server will use that to identify the user.

## Domain Name System (DNS)  


This is a hierarchical and decentralized naming system for computers, services, or any resource really that's connected to the internet and essentially it translates human readable domain names like tcmsec.com to IP addresses like 93.182.211.34 for example

DNS is like a phone book for the internet e.g u know the person name  the domain tcmsec.com but you want their phone number which is their ip address. and u look in to the phone to find the number

Domain have a hierarchical structure so top level domain which is .com .net .org .co .uk for example  
Second level domain which is the tcmsec.com and then we have subdomains so www.tcmsec.com or dev in dev.tcmsec.com  
<br/>

Finally we have DNS  records and there are few different types of these. Essentially they provide information about domains so an **A record** for example maps domain name to an ipv4 address. **An mx record** specifies the mail server for a domain, and **cname record** creates and alias for a domain name there are other record types  and also caching is an important part of DNS