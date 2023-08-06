---
title: Stored XSS via cloud attachment
author: Luigi Gubello
type: post
date: 2018-01-20T17:16:52+00:00
url: /blog/stored-xss-via-cloud-attachment/
categories:
  - Bug Bounty
  - English
  - XSS

---
**ZOHO Mail** is a business mail that includes integrated calendar, contacts, notes, and tasks apps. Initially I was looking for a stored XSS in the webmail, but I did not find it so I started checking the other services. I wondered if it was possible to inject malicious code via attachments in ZOHO Notes. By attaching a local file it wasn't, but in ZOHO Notes you can attach files from some cloud services: **Google Drive**, **Dropbox**, **Box** and **Evernote**. The XSS filters and protections worked well for the first three services, but not for Evernote. To my surprise it was possible to run javascript code in **gadgets.zoho.eu** via Evernote attachments.

![Stored XSS in gadgets.zoho.eu](/images/2018/01/zoho_alert_notebook.png#center)

There were two problems: the notebook title and the note title. By injecting the code in notebook title or note title, it ran into gadgets.zoho.eu. The first problem was quite serious, because the code ran as soon as the user opened Evernote via ZOHO and the payload was not visible. The second problem was similar, but the code ran when the victim clicked on the note-payload, and the payload was clear in the note title.

![XSS payload in the source code of *.zoho.eu](/images/2018/01/zoho_alert_note.png#center) 

I reported the bug via **ZOHO Bug Bounty**. The support was quick to answer me and fix the vulnerability, they asked me a question: **is it a self XSS?**

**No**. It may look like a self XSS because the victim logs into their own Evernote account, therefore it may seem that only they can type some code into their own Evernote account. Evernote was born to share easy notes and collaborate with other users, so in a shared job another user with sufficient privileges can change the title of a notebook or the title of a note and inject malicious code. If the payload was in the notebook title, it was not visible and the code ran as soon as Evernote was opened via ZOHO Notes.

**18/12/2017** - I send the report  
**27/12/2017** - The vulnerability is fixed and the bug bounty rewards are 100$ and my name in the [Hall of Fame](https://bugbounty.zoho.com/bb/info#hof) 
**20/01/2018** - Public disclosure

 