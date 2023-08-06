---
title: Javascript Injection in six Android mail clients
author: Luigi Gubello
type: post
date: 2020-02-15T10:26:54+00:00
url: /blog/javascript-injection-in-six-android-mail-clients/
categories:
  - Bug Bounty
  - English
  - IT Security
  - XSS

---
During last spring (2019) I started to "open and read" the Android applications before installing them. Reversing an APK file can be interesting to understand how an app works, how it manages the permissions and my data, if there are vulnerabilities. I was looking for a different Android mail client, so I started to reverse them and I found many mail clients on Play Store were - maybe are - vulnerable to Javascript injection. I found eight important apps vulnerable to cross-site scripting: **Newton Mail** 10.0.23, **Nine Email** 4.5.3a, **Blue Mail** 1.9.5.36, **Edison Email** 1.7.1, **Email TypeApp** 1.9.5.35 and **Spark** 2.0.2 + **two** apps I can't disclose now. In April and May 2019 I wrote to vendors of these apps, but only someone replied to me.

### Javascript Injection in Android Webview

Javascript injection in Android WebView is a serious vulnerability because in some scenario it was possible to execute code remotely by injecting a malicious Javascript code in the WebView (**CVE-2012-6636**, **CVE-2013-4710**). These vulnerabilities were fixed by Google, but Javascript injection in the WebView is yet a common bug, also for this reason Google have created [a support page][1] to explain how to use Javascript interfaces in the WebView. Although Javascript injection usually doesn't lead to code execution, it is still a serious vulnerability [because can be used to steal data][2] (similar to CVE-2019-11730 PoC) if `setAllowUniversalAccessFromFileURLs` is set `True`.

#### Newton Mail

**App:** Newton Mail  
**Version:** 10.0.23  
**Downloads:** +1.000.000  
**Has vendor replied? <span style="color: #00ff00;">Yes</span>**  
**CVE:** 2019-12365

{{< youtube HFS0eDaJ7to >}}
&nbsp;
  
In Netwon Mail 10.0.23 `setAllowUniversalAccessFromFileURLs` is set `True`.

#### Edison Mail

**App:** Edison Mail  
**Version:** 1.7.1  
**Downloads:** +1.000.000  
**Has vendor replied? <span style="color: #00ff00;">Yes</span>**  
**CVE:** 2019-12368

{{< youtube a9MgZz6iaRs >}}
&nbsp;
  
In Edison Mail 1.7.1 `setAllowUniversalAccessFromFileURLs` is set `True`.

#### Nine - Email & Calendar

**App:** Nine - Email & Calendar  
**Version:** 4.5.3a  
**Downloads:** +1.000.000  
**Has vendor replied? No**  
**CVE:** 2019-12366

{{< youtube QBBJPjIL9Gg >}}
&nbsp;

#### Spark

**App:** Spark  
**Version:** 2.0.2  
**Downloads:** +500.000  
**Has vendor replied? Yes**  
**CVE:** 2019-12370

{{< youtube FIcH7iiEWcI >}}
&nbsp;

#### Blue Mail

**App:** Blue Mail  
**Version:** 1.9.5.36  
**Downloads:** +5.000.000  
**Has vendor replied? No**  
**CVE:** 2019-12367

{{< youtube AhGvtlt45CM >}}
&nbsp;

#### TypeApp Email

**App:** TypeApp Email  
**Version:** 1.9.5.35  
**Downloads:** +1.000.000  
**Has vendor replied? No**  
**CVE:** 2019-12369

{{< youtube _tI_Yw04Xcs >}}
&nbsp;

 [1]: https://support.google.com/faqs/answer/9095419?hl=en-GB
 [2]: https://labs.integrity.pt/articles/review-android-webviews-fileaccess-attack-vectors/index.html