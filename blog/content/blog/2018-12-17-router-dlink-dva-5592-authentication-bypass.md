---
title: Router D-Link DVA-5592 – Authentication Bypass
author: Luigi Gubello
type: post
date: 2018-12-16T23:30:10+00:00
url: /blog/router-dlink-dva-5592-authentication-bypass/
categories:
  - English
  - IT Security

---
### Info

**Vendor:** D-Link Italia  
**Product:** [Router DVA-5592][1]  
**Firmware:** DVA-5592_A1_WI_20180823  
**CVE:** [2018-17777][2]  
**Shodan:** [ADB Broadband HTTP Server" title:"D-Link"][5]

### Description

In the router D-Link DVA-5592 it is possible to bypass the web authentication form. The problem is the path **/ui/cbpc/login**, because it is accessible without authentication. If the router's owner has not changed the Parental Control PIN, it is possible to access to the Parental Control area, by using the default PIN code. Now, by editing the path of the cookie **sid**, the login form can be bypassed.

{{< youtube zeWaL19fh5Y >}}
&nbsp;

### Mitigation

There is not an official patch for this vulnerability, but you can mitigate it by setting a different Parental Control PIN code.

**06/07/2018** – E-mail to D-Link  
**21/08/2018** – E-mail to D-Link via [contact form][3]  
**31/08/2018** – E-mail to ADB Global via [contact form][4]  
**03/09/2018** – E-mail to ADB Global  
**12/09/2018** – ADB replies that it is not its product  
**17/09/2018** – D-Link replies that they have reproduced the vulnerability  
**17/12/2018** – Public disclosure

* * *

Questo router è venduto in Italia da un importante operatore telefonico, quindi è abbastanza comune sul territorio italiano, specialmente per uso domestico. Se utilizzate questo router per offrire  una connessione wi-fi a molte persone consiglio vivamente di modificare il PIN dell'area "_Controllo genitori_" all'interno del pannello di controllo del router. Infatti se ci si riesce a connettere al router è possibile bypassare la schermata di login, compromettendo il dispositivo.


{{< youtube uW6NeOwA9RI >}}
&nbsp;

 [1]: https://eu.dlink.com/it/it/products/dva-5592-modem-router-wifi-voip-vdsl-adsl-ac2000
 [2]: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-17777
 [3]: https://eu.dlink.com/it/it/contact-d-link
 [4]: https://www.adbglobal.com/about-adb/contact/
 [5]: https://www.shodan.io/search?query=%22ADB+Broadband+HTTP+Server%22+title%3A%22D-Link%22