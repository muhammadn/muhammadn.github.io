---
layout: default
title:  "Fighting HTTP/Web spammers with ModSecurity"
date:   2021-11-24 22:11:00 +0800
---

Recently i had encountered many spam registrations and web comments on one of my client's website. Normally a configuration in Apache or Nginx should suffice but it got really bad that i had to automate to ban those abusive spammers off the website.

So instead of doing it like a chore to manually add those abusive IP addresses to be blocked, i used modsecurity which is already configured beforehand on our websever.

The code snippet below checks for the web user's IP address against blacklists - sbl/xbl is for IP addresses which has malware/or compromised machines and projecthoneypot (httpbl) also checks for abusive IP sources.

```
# Block Spam Regisrations/Web comments
# Ban for 7 days
SecAction "id:400000,phase:1,initcol:ip=%{REQUEST_HEADERS:X-Forwarded-For},pass,nolog"
SecRule IP:spam "@gt 0" "id:400001,phase:1,chain,deny,status:403,msg:'Spam host %{REQUEST_HEADERS:X-Forwarded-For} already blacklisted'"
SecRule REQUEST_METHOD "POST" "chain"
SecRule REQUEST_URI "/your/website/post/comment/path"

# spamhaus
SecRule REQUEST_URI "/your/website/post/comment/path" "id:400010,chain,deny,log,status:403,msg:'Spam host %{REQUEST_HEADERS:X-Forwarded-For} detected by sbl-xbl.spamhaus.org'"
SecRule REQUEST_METHOD "POST" "chain"
SecRule REMOTE_ADDR "@rbl sbl-xbl.spamhaus.org" "setvar:IP.spam=1,expirevar:IP.spam=604800"

# httpbl
# get your httpblkey by registering at https://www.projecthoneypot.org/
SecHttpBlKey yourhttpblkey
SecRule REQUEST_URI "/your/website/post/comment/path" "id:400012,chain,deny,log,status:403,msg:'Spam host %{REQUEST_HEADERS:X-Forwarded-For} detected by dnsbl.httpbl.org'"
SecRule REQUEST_METHOD "POST" "chain"
SecRule REMOTE_ADDR "@rbl dnsbl.httpbl.org" "setvar:IP.spam=1,expirevar:IP.spam=604800"

# Blacklisted IP Address file
SecRule REQUEST_URI "/your/website/post/comment/path" "chain,id:400020,log,deny,status:403,msg:'Spam host %{REQUEST_HEADERS:X-Forwarded-For} listed in blacklist file'"
SecRule REQUEST_METHOD "POST" "chain"
SecRule REMOTE_ADDR "@ipMatchFromFile /etc/nginx/blacklisted_ips.txt" "setvar:IP.spam=1,expirevar:IP.spam=604800"
```

The last part with blacklisted IPs (in blacklisted_ips.txt) which a file where you can declare the IP blocks if you need to. example:

```
123.123.12.0/24
12.34.0.0/16
44.44.44.44/32
```

To make it more automated, i would check for failed attempts at sending the POST request with a counter that blocks the IP address once it reaches the counter limit:

```
# Check that this is a POST
SecRule REQUEST_METHOD "POST" "id:500010,phase:5,chain,t:none,nolog,pass"
# AND Check for authentication failure and increment counters
SecRule RESPONSE_STATUS "^500" \
 "chain,setvar:IP.bf_counter=+1"
SecRule REQUEST_URI "/your/website/post/comment/path"

# Check for too many failures from a single IP address. Block for 10 minutes.
SecRule IP:bf_counter "@ge 5" \
   "id:500002,phase:5,pass,t:none, \
   setvar:IP.bf_block,\
   setvar:!IP.bf_counter,\
   expirevar:IP.bf_block=604800"

```

Also the above snippet is also useful when you want to block abusive users who tries to do login bruteforce attacks
