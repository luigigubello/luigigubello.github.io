---
title: HTML Injection in Signal Desktop 1.10.1
author: Luigi Gubello
type: post
date: 2018-05-16T15:22:36+00:00
url: /blog/html-injection-in-signal-desktop/
categories:
  - English
  - IT Security
draft: true
---
A few days ago some researchers discovered an HTML Injection vulnerability in Signal Desktop and they wrote a [public disclosure][1]. The Signal team quickly released an update on May 11th, the problem was in the file **/js/views/message_view.js**.

Reading the changes to **message_view.js**, it seemed that the Signal team had only fixed the "[problem of the URL][2]". So, maybe, I could still inject HTML code somehow. In Signal Desktop there are not many features, so I have tried to write me a basic message: `<b>PROVA</b>`. Obviously nothing happened. So I have tried to use the feature **Reply to message**, and BINGO! My original message `<b>PROVA</b>` has become **`PROVA`**. So, it was still possible to inject HTML code in Signal Desktop. I have tried to run javascript code, but it was blocked from the [Content Security Policy][3], like in [the first report][1]. To run javascript code in the victim's Signal Dekstop, first the victim must download a malicious HTML / js file, then the attacker can run it with the **src** attribute. Obviously the attacker must know the file path on the victim's computer. Another interesting attack, without javascript, is to use **ping** attribute to find the IP address of the victim (thanks to Fabrizio Carimati ([**@clodo76**][4]) for this idea).

The vulnerability is the same of the first report, the difference is that the attacker must send two messages: the first message is the HTML code, the second message is the reply to the first to execute the code. I found this vulnerability on May 15, but a few hours later it was fixed with an update **(v 1.11.0)**, before I sent the report to the Signal developers. Someone ([@mis2centavos][5]) [tweeted about the new vulnerability][6] a few hours before me.

### Proof of Concept

{{< youtube YtVKHj_b4r0 >}}
&nbsp;

**15/05/2018** - Signal Desktop is updated to version **1.11.0**

 [1]: https://ivan.barreraoro.com.ar/signal-desktop-html-tag-injection/
 [2]: https://twitter.com/Clodo76/status/995284143411007488
 [3]: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP
 [4]: https://twitter.com/Clodo76
 [5]: https://www.twitter.com/mis2centavos
 [6]: https://twitter.com/mis2centavos/status/996285724277329920