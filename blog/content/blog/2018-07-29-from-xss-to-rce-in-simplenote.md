---
title: From XSS to RCE in Simplenote 1.1.3
author: Luigi Gubello
type: post
date: 2018-07-29T16:20:59+00:00
url: /blog/from-xss-to-rce-in-simplenote/
categories:
  - Bug Bounty
  - English
  - XSS

---
### Summary

In **Simplenote 1.1.3 - Desktop app** there is a stored XSS vulnerability that can be used to execute arbitrary code. If there is malicious code in the note and the user tries to print it (for example to save it as a PDF), the malicious code runs.

{{< youtube YzEzLsSAO3s >}}
&nbsp;
  
**[#358049 - RCE via Print function [Simplenote 1.1.3 - Desktop app]][1]**

**27/05/2018** – I send the report  
**25/06/2018** – The vulnerability is fixed and the bug bounty reward is 250$  
**26/07/2018** – Public disclosure

* * *

I suggest these awesome readings about XSS and Remote Code Execution in the Electron based applications:

  * [Taking note: XSS to RCE in the Simplenote Electron client](https://ysx.me.uk/taking-note-xss-to-rce-in-the-simplenote-electron-client)
  * [Only an Electron Away from Code Execution](https://silviavali.github.io/Electron/only_an_electron_away_from_code_execution)

 [1]: https://hackerone.com/reports/358049