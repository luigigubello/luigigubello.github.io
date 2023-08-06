---
title: Stored XSS in Microsoft Bing
author: Luigi Gubello
type: post
date: 2018-04-21T10:30:51+00:00
url: /blog/stored-xss-in-microsoft-bing/
categories:
  - Bug Bounty
  - English
  - XSS

---
After many unsuccessful attempts to find an XSS in Yahoo's domains, I decided to move my attention to Microsoft Bing.  
If you have a Microsoft account, Bing allows you to save online content (images, videos and places) on the page _My saves_, and allows to create collections to better manage your own content.  
The titles of these collections were not properly filtered, so it was possible to break the code and inject persistent arbitrary code.  
The code could be injected easily, all it took was the wrong image added to _My saves_.  
I was lucky with Bing, now I can go back to fail with Yahoo 🙂

![Stored XSS in Microsoft Bing](/images/2018/04/stored_xss_bing.png#center)

### Attack Scenario

An attacker pastes on a site (for example a popular blog) the link of an image found on Bing. The GET parameter `q` has a malicious javascript code. Example:

`www.bing.com/images/search?view=detailV2&ccid=ugtTKk%2bf&id=77C601FDB8F3CAC089D09B4F80E5984B3D66972F&thid=OIP.ugtTKk-ffUvl93ztAPX8mAHaD8&q=malicious_javascript&simid=608017275115016031&selectedIndex=0&ajaxhist=0`

A victim visits this site randomly, sees the image, opens it and saves it in _My Saves_. Bing uses the value of variable `q` to give a title to the new collection. The malicious code runs whenever the victim opens _My saves_.  
**In this attack there is no interaction between the attacker and the victim.**

### Proof of Concept

{{< youtube SJ8c27mpN-c >}}
&nbsp;

**16/04/2018** – I send the report  
**19/04/2018** – The vulnerability is fixed and I’m rewarded by having my name written in the Hall of Fame  
**21/04/2018** – Public disclosure

