---
title: My Calendar 2.5.16 – Authenticated stored XSS
author: Luigi Gubello
type: post
date: 2018-04-18T08:13:39+00:00
url: /blog/my-calendar-2-5-16-authenticated-stored-xss/
categories:
  - English
  - WordPress
  - XSS
draft: true
---
### Info

**Product:** My Calendar  
**Version:** 2.5.16  
**Active installations:** 30,000+  
**Product page:** <https://it.wordpress.org/plugins/my-calendar/>

### Description

An authenticated user, who can add new events,  can inject arbitrary javascript code via **event_time_label** input. The arbitrary code runs both on the event page and in the admin panel.

In **my-calendar-event-manager.php**, **line 1873**, the variable `$eventTime` is not sanitized.

### Proof of Concept

{{< youtube OvoEiJd6ggY >}}
&nbsp;
  
My Calendar 2.5.16 is vulnerable, probably earlier versions too. Joe Dolson, My Calendar's author, was really quick to fix the vulnerability and update the plugin.

**02/04/2018** - I send the report  
**03/04/2018** - My Calendar is updated to version **2.5.17** and the vulnerability is fixed  
**18/04/2018** - Public disclosure