---
title: Using curl to download tricky stuffs with bash
date: 2018-08-05 13:02:12
tags:
- English
- Linux
---
Hope that you guys still remember how proxy servers work,
![Like this!](/images/curl/Proxy_concept_en.png)

Sometimes you want to access to a website that is very slow, but you do have a server that can access to the website very fast, and you can also access to the server very fast, then you can use the server as a "proxy".

The problem I encountered now is that I want to download some stuff (Circuit de la Sarthe, Le Mans Track) which is around 300MB. However, the speed is unacceptably slow when I try to download it. That being said, I do have a server running FTP located in the UK, which may be a very good proxy server.

So, the first thing I tried to do is just gather the link and `wget` it. This actually works for a lot download links (those which you can download freely), such as ubuntu images, but not for this one.

It requires you to log in in order to download.
![Like this!](/images/curl/sc1.png)

The website uses cookies to store your login information, as a lot of you have already known, and since`wget` is a simple tool, although it can be used with cookies, this could be a bad plan.

The good news is that firefox supports tracking every network package you send and convert it into `curl` commands.

`curl` is an amazing stuff for transferring data with URLs. It can send requests and receive responses. Google it for further details, but we only need to use it for now!

![Like this!](/images/curl/sc2.png)

Hit F12 to and go to `Network` to start monitering your ... network. Then hit download, and you will see a GET request.

![Like this!](/images/curl/sc3.png)

Then, what you want to do is wait until it returned an HTTP 200 OK, then right click and copy cURL of it.

```
curl 'https://www.racedepartment.com/downloads/circuit-24h-lemans-2017.2482/download?version=38572' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:61.0) Gecko/20100101 Firefox/61.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' -H 'Accept-Language: en-GB,en;q=0.5' --compressed -H 'Referer: https://www.racedepartment.com/downloads/circuit-24h-lemans-2017.2482/' -H 'Cookie: xf_session=xxxxxxxxxxxxxxxxxxxxxxxxx; _ga=GA1.2.1109830400.1533447313; _gid=GA1.2.16666612.1533447313; _cmpQcif3pcsupported=1; __gads=ID=f3xxxxxx1c8:T=1533447590:S=ALNI_MabyxxxxxxxxbCxxxxxxxJ5AiA; OX_plg=pm; xf_notice_dismiss=-1' -H 'Connection: keep-alive' -H 'Upgrade-Insecure-Requests: 1' -H 'Pragma: no-cache' -H 'Cache-Control: no-cache'
```

It should be something like this, containing all data (request, cookies, etc.) sent to the server. It could be sensitive!

Add argument `--output lemans.zip` to make sure things are written to your file and don't mess up your terminal.

Put that into your server's terminal and it should return something... unexpected. Remove the argument and you will see that "you changed your ip so that you need to login again.

Well, well, well. Seems that we need to do the same thing again: but login this time. 

Note that we are assuming the session ID will always be the same for every login attempt (and it is for this site). But the thing might be different! If so, you will need to also record the returned cookies and fill them into the cURL command, which I'll not demonstrate here.

![Like this!](/images/curl/sc4.png)

What we need to do is to login as normally, but monitor the request and do the same thing - copy as cURL. Then go to your server and execute it to "login" on your server.

Then, execute the download cURL with parameter, then enjoy your download!

As I said, this server has a ftp running, so I downloaded the file to my local disk using ftp easily. Cool!