---
title: WP Live Chat Support 8.0.05 – Stored XSS
author: Luigi Gubello
type: post
date: 2018-04-08T22:48:12+00:00
url: /blog/wp-live-chat-support-8-0-05-stored-xss/
categories:
  - English
  - WordPress
  - XSS

---
### Info

**Product:** WP Live Chat Support  
**Version:** 8.0.05  
**Active installations:** 50,000+  
**Product page:** <https://wordpress.org/plugins/wp-live-chat-support/>  
**CVE:** [2018-9864][1]

### 1.

### Description

An unauthenticated user could inject arbitrary javascript code in the admin panel by using the text field `Name` of WP Live Chat Support.

Using a single input point it was possible to inject javascript code into two different output points of the admin panel. There were two issues in the external javascript file [**bleeper-agent-dev.js**][2]:

  * the function **bleeper_strip_tags** filtered closed tags only, so it could be bypassed with an unclosed tag
  * the variable **chatInfoArea-Name** was not escaped

This vulnerability has been fixed in all versions of the plugin without an update because `bleeper-agent-dev.js` is an external file and the developer has updated it.

### Proof of Concept

{{< youtube kAW1zU3PI00 >}}
&nbsp;

### 2.

### Description

An unauthenticated user can inject arbitrary javascript code in the admin panel by using the text field `Name` of WP Live Chat Support. The arbitrary code runs on the page **wplivechat-menu-history**.

In the file **wp-live-chat-support.php **there is no sanitization of **$result->id** (row 4439).  
WP Live Chat Support 8.0.05 is vulnerable, probably earlier versions too.

### Proof of Concept

{{< youtube eHG1pWaez9w >}}
&nbsp;

### 3.

### Description

An unauthenticated user could inject arbitrary javascript code by attaching an SVG file in the chat. This is a feature of WP Live Chat Support **Pro**.

The problem was the array **bleeper_file_suffix_check**, a white list of the file extensions, in the external javascript file **bleeper-agent-dev.js**. In this array there was the ***.svg** extension, for this reason it was possible to attach SVG files.

This vulnerability has been fixed in all versions of the plugin without an update because `bleeper-agent-dev.js` is an external file and the developer has updated it.

### Proof of Concept

{{< youtube AYmLSmpTA6I >}}
&nbsp;

**06/03/2018** - I send the report of the first vulnerability  
**09/03/2018** - The developers reply me and they give me a _Pro_ version to look for other vulnerabilities  
**11/03/2018** - I send the report about other vulnerabilities  
**23/03/2018** - WP Live Chat Support is updated to version **8.0.06** and the second vulnerability is fixed. The other vulnerabilities are remotely fixed  
**09/04/2018** - Public disclosure

 [1]: https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-9864
 [2]: https://bleeper.io/app/assets/js/bleeper-agent-dev.js