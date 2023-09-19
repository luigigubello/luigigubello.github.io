---
title: GD bbPress Attachments 2.5 – Authenticated stored XSS
author: Luigi Gubello
type: post
date: 2018-05-13T23:58:19+00:00
url: /blog/gd-bbpress-attachments-2-5-authenticated-stored-xss/
categories:
  - English
  - WordPress
  - XSS

---
### Info

**Product:** GD bbPress Attachments  
**Version:** 2.5  
**Active installations:** 10,000+  
**Product page:** <https://it.wordpress.org/plugins/gd-bbpress-attachments/>

### Description

An authenticated user of a bbPress forum, who can attach a file, can inject arbitrary javascript code via filename. The arbitrary code runs both on the topic page and in the admin panel, and it only affects the administrators, moderators and the attacker.

The variable `$error['file']` in **/code/****attachments/front.php** (line 349) is not escaped.

### Proof of Concept

{{< youtube n4xX0ODV1O4 >}}
&nbsp;

GD bbPress Attachments 2.5 is vulnerable, probably earlier versions too.


**24/04/2018** – I send the report  
**27/04/2018** –[GD bbPress Attachments is updated to version **2.6**][1] and the vulnerability is fixed  
**14/05/2018** – Public disclosure

 [1]: https://www.dev4press.com/blog/plugins/2018/gd-bbpress-attachments-2-6/