---
title: 'Bookly #1 WordPress Booking Plugin (Lite) 13.2 – Blind Stored XSS'
author: Luigi Gubello
type: post
date: 2018-02-09T23:01:23+00:00
url: /blog/bookly-blind-stored-xss/
categories:
  - English
  - WordPress
  - XSS

---
### Info

**Product:** Bookly #1 WordPress Booking Plugin (Lite Version)  
**Version:** 13.2  
**Active installations:** 10,000+  
**Product page:** https://wordpress.org/plugins/bookly-responsive-appointment-booking-tool/   
**CVE:** [2018-6891](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-6891)

### Description

An unauthenticated user can inject arbitrary persistent javascript code in the admin panel.

### Proof of Concept

{{< youtube 3H-wCK4juSU >}}

&nbsp;

Bookly Lite 13.2 and Bookly Pro 14.5 are affected, probably even earlier versions.  
_I think the problem is that jQuery.ajax request is not sanitized in **ng-payment_details_dialog.js**. [*]_

**07/01/2018** - I send the report  
**26/01/2018** - Bookly Lite is updated to version **14.5** and the vulnerability is fixed  
**10/02/2018** - Public disclosure

_[*] I have been very busy these days, so I could not read the code of the plug-in._
