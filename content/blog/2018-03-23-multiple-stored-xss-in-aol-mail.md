---
title: Multiple stored XSS in AOL Mail
author: Luigi Gubello
type: post
date: 2018-03-23T18:05:32+00:00
url: /blog/multiple-stored-xss-in-aol-mail/
categories:
  - Bug Bounty
  - English
  - XSS

---
In November, I reported various persistent XSS vulnerabilities in **AOL Mail** to the AOL Security Team. They replied quickly and fixed the vulnerabilities in less than 90 days.

### 1.

Using an unclosed tag, it was possible to inject arbitrary javascript code. The payload ran as soon as the victim opened the site **mail.aol.com** because the code was in the e-mail preview.

{{< youtube o2_6lSht9L4 >}}
&nbsp;
  
**18/11/2017** - I send the report  
**28/11/2017** - The vulnerability is fixed and I'm rewarded by having my name written in the [Hall of Fame][1]

After the update of the desktop version, I contacted the security team again because the mobile version was not fixed.

{{< youtube mc2EVlz8ZsY >}}
&nbsp;
  
**28/11/2017** - I send a new report  
**12/12/2017** - The vulnerability is fixed  
**23/03/2018** - Public disclosure

### 2.

It was possible to inject javascript code via vCard import. This vulnerability was closed before I submitted the report. Well done!

{{< youtube DRaBAt6eRQg >}}
&nbsp;
  
**23/03/2018** - Public disclosure

### 3.

In the mail contacts it was no longer possible to inject any code, but if a text field of the vCard contained javascript code and the victim clicked on the Printer icon then the payload still ran. This vulnerability was less severe than the first, but I reported it to the security team anyway.

![Blind stored XSS in AOL Mail via vCard import](/images/2018/03/aol_stored_xss_print.png#center)

**18/12/2017** - I send the report  
**21/02/2018** - The vulnerability is fixed  
**23/03/2018** - Public disclosure

_That's all folks!_

 [1]: https://contact.security.aol.com/hof/
 [2]: /images/2018/03/aol_stored_xss_print.png