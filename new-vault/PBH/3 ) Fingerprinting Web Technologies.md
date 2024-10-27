
**RECON IS EVERYTHING**

- *Digging Deep Enough Finding Assets That Are Not On Main Web Page Cuz Everyone Look At It*

- First Review The Scope OF The Program
- Check For The Wild Card
- Review What is Out Of SCOPE

==HOW TO IDENTIFY WEBSITE TECHNOLOGY==

*its the methodology not the tools*
	*tools come and go*

1. **builtwith.com** -> It give us all of their technology profiles
2. **Wappalyzer** ->  web analyzer everything that been use automatically
3.  Grab as much information

**KALI LINUX TOOLS**
`curl -I https://azena.com`
There's a redirect we need to follow it -> 302
`curl -I -L https://azena.com`
You can see *Server Info & Security Header*
The clean way of doing it just use `securityheaders.com`
*NMAP*
`nmap -p443 -A azena.com`
`nmap -p443 --script=http-server-header azena.com`
*Nmap for open ports*
`namp -p80,443 -A azena.com`

#  ==Directory Enumeration and Brute Forcing==

FFUF -> Directory Brute Forcing Tool
`ffuf -w wordlist:FUZZ -u url/FUZZ`
Recursive -> Directory into Directory
```dirb ip addr```
This use its own wordlist and scan recursively
Another tool is `dirbuster&`
Look for the extension also php .html

##   ==Subdomain Enumeration==

Target -> `azena.com`
subdomain -> `dev.azena.com`
wildcard -> `*.azena.com`
search on google -> `site:azena.com`
Lets reduce the search result
`site:azena.com -www -store` 
or you can search for file `site:azena.com filetype:pdf`
or xlsx, docx, doc

**CRT.SH**
Website for wild card searching
`%.azena.com`

**SUBFINDER**
`subfinder -d azena.com`

**ASSETFINDER**
`assetfinder azena.com`

GREP ONLY `AZENA.COM`
`assetfinder azena.com | grep azena.com | sort -u > azena2.txt`

**AMASS**
`amass enum -d azena.com > azena3.txt`

Sorting
`cat azena | sort -u > azenasorted.txt`
`cat azenasorted.txt >> azena2.txt `
`cat azena2.txt | sort -u > azenafinal.txt`

**CHECKING ALIVE SUBDOMAIN USING `HTTPPROBE`**

`cat azenafinal.txt | grep azena.com | sort -u | httpprobe -prefer-https |grep https > azenaalive.txt`

**TOOL GOWITNESS**
`mkdir azenapics` -P screen shot path
remove `https://` from file azenaalive.txt  By opening the file in mousepad and
`mousepad azenaalive.txt`
Find or replace control r type `https://` and remove it
`gowitness file -f azenaalive.txt -P azenapics --no-http `

# ****==##   Burp Suite Overview==

Play around -> get comfortable with the intercepting traffic
Looking At Repeater Looking At Intruder
Knowing what it takes etc etc
TryHackMe -> Rooms
