---
title: Events Manager 5.8.1.1 – Stored XSS
author: Luigi Gubello
type: post
date: 2018-03-25T22:01:17+00:00
url: /blog/events-manager-authenticated-stored-xss/
categories:
  - English
  - WordPress
  - XSS

---
### Info

**Product:** Events Manager  
**Version:** 5.8.1.1  
**Active installations:** 100,000+  
**Product page:** <https://it.wordpress.org/plugins/events-manager/>  
**CVE:** [2018-9020][1]

### Description

An unauthenticated user or a user without privileges, who can submit an event, can inject javascript code in the Google Maps miniature. The malicious code runs in the admin panel when a user with privileges opens the submitted event.

The problem is in the file **events-manager.js**, the variable **mapTitle** is not escaped.

### Proof of Concept

{{< youtube 40d7uXl36O4 >}}
&nbsp;

Events Manager 5.8.1.1 is vulnerable, probably earlier versions too.

Marcus Sykes, the Events Manager's developer, fixed the vulnerability on January, 15th, and published a post on his blog [about it][2].

**10/01/2018** - I send the report  
**15/01/2018** - Events Manager is updated to version **5.8.1.2** and the vulnerability is fixed  
**26/03/2018** - Public disclosure

 [1]: https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-9020
 [2]: http://wp-events-plugin.com/blog/2018/01/15/events-manager-5-8-1-2-security-release/