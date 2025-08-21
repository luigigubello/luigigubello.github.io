---
title: "Remote Code Execution in Logseq (just another XSS to RCE in an Electron app)"
author: Luigi Gubello
type: post
date: 2025-08-21T16:00:53+02:00
url: /blog/logseq-rce-via-pdf/
categories:
  - English
  - IT Security
  - XSS
  - Clojure

---
Working in [Pitch](https://www.linkedin.com/company/pitchhq/), I had the opportunity to read, review, and code in Clojure and Clojurescript. [Clojure](https://clojure.org/) is a niche programming language, a Lisp language hosted on a JVM, and it has a small, but "senior", community. [ClojureScript](https://clojurescript.org/index) is a variant of Clojure that compiles to JavaScript (instead of JVM), so it is a sort of Clojure for the JavaScript ecosystem, with (almost) the same paradigms and structures.

From a security engineer's perspective, this programming language is challenging for at least two _non-technical_ reasons:

- **The Clojure open source community is small, and the Clojure security community is smaller, sometimes invisible.** The developers who can properly contribute to a Clojure codebase are rare, and security engineers (or security-focused contributors) who can review the code are rarest. This leads to a natural lack of eyes on Clojure projects. 
- **Not enough code, not enough issues.** A small number of known vulnerabilities or CVEs doesn't necessarily mean the code is more secure, it often means not enough eyes are reading and reviewing the code. And if you can't read enough security issues, vulnerability reports, and patch commits written by others, it becomes harder - and slower - to learn how to avoid similar mistakes and how to mitigate them.

This isn't the first time I report a security issue to Logseq's maintainers. In August 2022, I disclosed another cross-site scripting vulnerability ([#6291](https://github.com/logseq/logseq/issues/6291)). At that time, my approach was more "black-box" oriented: find the issue, report the issue. If I had the time and interest to review the code to understand the root cause, great!; otherwise, I just reported it. This issue, even if not well written (re-reading it now), can still help understand how some web applications written in ClojureScript and Clojure can process user input. In addition to common payloads, a penetration tester can also use [Hiccup](https://github.com/weavejester/hiccup)-based payloads. 

If you are interested in the security of Clojure web applications, I recommend Aaron Bedra's old but still gold talk: [clojure.web/with-security](https://aaronbedra.com/presentation/clojure_web_security/) (2014). 

Recently, I've found a cozy pleasure which reduced my stress: reviewing open-source code and reading GitHub issues, directly from my mobile phone. So, let's take a closer look at this new vulnerability in Logseq Desktop.

## Technical details

All the technical comments refer to the commit [`fa869a4`](https://github.com/logseq/logseq/tree/fa869a4).

Logseq Desktop is an Electron application written mainly in Clojure and ClojureScript, which means the maintainers rely, in a certain extent, on Clojure, Maven, and NPM third-party packages. In the Electron applications, a common approach to find critical vulnerabilities can be summarized as: _"Find a cross-site scripting vulnerability and escalate the scope through a misconfiguration in the Electron framework"_.

In the file `window.cljs`, there is the Electron configuration ([window.cljs#L41-L46](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/src/electron/electron/window.cljs#L41-L46)) for the main windows, and it is mostly secure. It is almost the same for `"about:blank"` windows launched via `window.open()`.

```clojure
:webPreferences
{:plugins                 true        ; pdf
:nodeIntegration         false
:nodeIntegrationInWorker false
:nativeWindowOpen        true
:sandbox                 false
:webSecurity             (not dev?)
:contextIsolation        true
:spellcheck              ((fnil identity true) (cfgs/get-item :spell-check))
:enableBlinkFeatures     'OverlayScrollbars'
:preload                 (node-path/join js/__dirname "js/preload.js")}
```

The `nodeIngration` and `nodeIntegrationInWorker` are disabled, and the `contextIsolation` is enabled, which means the renderer process cannot access Node.js APIs, and the preload script (`preload.js`) runs in an isolated context from the renderer one. So, I cannot pop up a calculator using Node.js APIs, even if I find a JavaScript code injection vulnerability. 

Let's check the sanitization methods in place to prevent cross-site scripting vulnerabilities. Logseq renders HTML code using [Hiccups](https://github.com/teropa/hiccups), a ClojureScript port of Hiccup, and sanitizes user input using DOMPurify, which is a solid solution when properly configured ([#6901](https://github.com/logseq/logseq/pull/6901)). In the file `block.cljs`, the re-usable function to render HTML code automatically sanitizes the output ([block.cljs#L1554-L1562](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/src/main/frontend/components/block.cljs#L1554-L1562)).

```clojure
(defn hiccup->html
  [s]
  (let [result (gp-util/safe-read-string s)
        result' (if (seq result) result
                    [:div.warning {:title "Invalid hiccup"}
                     s])]
    (-> result'
       (hiccups.core/html)
       (security/sanitize-html))))
```

The only tag allowed by the DOMPurify configuration is `<iframe>` (which could sound interesting, considering `:nativeWindowOpen true` in the Electron configuration) ([security.cljs](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/src/main/frontend/security.cljs)), and Logseq has an allowed list of protocols defined in the `preload.js` script ([preload.js#L8](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/resources/js/preload.js#L8)). In general, the Logseq editor appears to be solid enough against cross-site scripting vulnerabilities. 

Usually, I don't investigate third-party packages too much, but vulnerable NPM packages can be a good entry point for injecting arbitrary JavaScript code. Logseq Desktop has enabled "by default" some [official extensions](https://github.com/logseq/logseq/tree/fa869a4c774740c73a48e81828e89e643ff76f00/src/main/frontend/extensions) that can increase the surface attack. In particular, the custom PDF viewer and the whiteboard Excalidraw rely on two popular packages: `pdfjs-dist` and `@excalidraw/excalidraw`. Both packages were outdated and vulnerable to two known XSS vulnerabilities, [CVE-2024-4367](https://github.com/mozilla/pdf.js/security/advisories/GHSA-wgrm-67xf-hhpq) for `pdfjs-dist` ([package.json#L133](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/package.json#L133)) and [CVE-2024-32472 ](https://github.com/excalidraw/excalidraw/security/advisories/GHSA-m64q-4jqh-f72f) for `@excalidraw/excalidraw` ([package.json#L96](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/package.json#L96)). 

CVE-2024-4367 is the more promising one because the PDF viewer is loaded in the main window by default ([pdf/core.cljs#L1049-L1063](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/src/main/frontend/extensions/pdf/core.cljs#L1049-L1063)), **having full access to Logseq APIs**. So, now I have a JavaScript injection in the main window and access to Logseq APIs, but it is still not enough to escalate the scope: I need an OS command injection to run arbitrary commands. Logseq allows local note versioning and synchronization using `git`, enabling users to back up their notes to a GitHub repository. For a technical choice or reason I don't understand, Logseq Desktop invokes a shell directly to run `git` commands ([shell.cljs](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/src/main/frontend/handler/shell.cljs#L42-L61)). Now, you have already understood the end of this story 🥲.

Both `api.cljs` ([api.cljs#L907-L910](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/src/main/logseq/api.cljs#L907-L910)) and `git.cljs` ([git.cljs#L9-L12](https://github.com/logseq/logseq/blob/fa869a4c774740c73a48e81828e89e643ff76f00/src/main/logseq/sdk/git.cljs#L9-L12)) can run arbitrary Git CLI commands, and Git can be used to inject arbitrary commands. The final payload (for macOS) is:

```javascript
logseq.sdk.git.exec_command(['config','--global','alias.calc','!open -a Calculator']);
logseq.sdk.git.exec_command(['calc']);
```

### Proof-of-Concept

1. Download [PoC-logseq-CVE-2024-4367.pdf](static/docs/2025/08/PoC-logseq-CVE-2024-4367.pdf) and upload it as asset (or host it on a web server and link it in a Logseq page).
2. Open the file using Logseq Desktop on macOS.
3. When the PDF is rendered, the embedded JavaScript is executed, launching the Calculator app in macOS.

For creating the custom PDF payload, I used the Python script [LOURC0D3/CVE-2024-4367-PoC](https://github.com/LOURC0D3/CVE-2024-4367-PoC).

### Video

{{< youtube JCCg4kxSPpU >}}
&nbsp;

## Timeline

- **20/05/2025:** First attempt to contact the maintaners ([#11868](https://github.com/logseq/logseq/issues/11868)).
- **25/05/2025:** Report submitted via GitHub Security.
- **02/06/2025:** PDF.js update merged in `master` ([#11914](https://github.com/logseq/logseq/pull/11914)) - _but never released in any application build_.
- **16/08/2025:** Proposed 26th August as disclosure date (90 days later).
- **18/08/2025:** Maintainers disclosed the security issue. Currently, the last release (0.13) is **unpatched**.

### Resources

- [Awesome Electron.js hacking & pentesting resources](https://github.com/doyensec/awesome-electronjs-hacking)
- [Evernote RCE: From PDF.js font-injection to All-platform Electron exposed ipcRenderer with listened BrokerBridge Remote-Code Execution](https://0reg.dev/blog/evernote-rce)
