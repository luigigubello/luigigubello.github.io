---
title: Italian Hacker Camp 2018
author: Luigi Gubello
type: post
date: 2018-08-18T08:48:50+00:00
url: /blog/italian-hacker-camp-2018/
categories:
  - Italian
  - Other

---
Circa due settimane fa, a Padova, si è tenuto l'evento **[Italian Hacker Camp 2018][1]**, un vero e proprio campeggio per appassionati del mondo dell'informatica (ma non solo). L'evento si è svolto dal 2 al 5 agosto e offriva alle persone, come si può intuire dal nome, la possibilità di fermarsi lì con la propria tenda e il proprio sacco a pelo per tutta la durata dell'evento. Non ho avuto modo di parteciparci in maniera così "selvaggia", ma sono riuscito a passarci in giornata, trovando un ambiente inclusivo, che non ha deluso le  mie aspettative. Cerco di semplificare la mia esperienza con due elenchi puntati, uno con gli aspetti personalmente positivi dell'evento e l'altro con qualche spunto per migliorare le edizioni future, nella speranza che ce ne siano.

**Lati positivi**

  * L'evento è stato inclusivo, anche grazie a un [codice di condotta][2]
  * L'evento era ad offerta libera
  * L'acqua era gratis
  * I talk non riguardavano solo tematiche strettamente informatiche, ma toccavano molti temi: il mondo open source, diritto digitale, giornalismo ed altri
  * **Ho visto persone suonare un Gameboy Color**
  * Venivano regalati molti adesivi da attaccare sul proprio portatile (o dove si vuole, ma a quanto pare gli adesivi sui portatili piacciono molto)

**Suggerimenti** [non richiesti, ma li scrivo ugualmente]

  * Permettere alle persone di pagare con la carta di credito: è scomodo dover girare con molto contante o spostarsi per prelevarlo
  * Migliorare il menù dei pasti, per dare una maggior scelta a tutti e rendere ancor più inclusivo quest'evento

Approfitto inoltre di questo post sul blog per allegare i due talk che ho portato a IHC2018, uno da 12 minuti intitolato _"Image-sharing sites and stored XSS: a serious problem" _e uno da 30 minuti intitolato _"The Diffie-Hellman protocol is weak for Fermat primes"_. Questa è stata per me un'esperienza nuova, perché sono i primi talk in assoluto che propongo, e ho dovuto affrontare una delle mie tante ansie, cioè quella di parlare di fronte a un pubblico.

### Image-Sharing Sites and Stored XSS: A Serious Problem

Questo talk da 12 minuti si basa su una bozza, mai pubblicata, in cui analizzavo la possibilità di iniettare codice Javascript in maniera persistente in alcuni grossi siti di condivisione delle immagini. Le slide mostrano alcune tecniche comuni per cercare vulnerabilità di tipo XSS persistente in un sito di image-sharing. Alla fine delle slide è presente un QR code contenete una "bibliografia".

  * [IHC2018 &#8211; Image-sharing sites and stored XSS: a serious problem [Download PDF]][3]

### The Diffie-Hellman Protocol is Weak For Fermat Primes

Questo talk, della durata di 30 minuti, è più complesso del precedente: contiene un argomento più ostico e ho trovato maggiori difficoltà nel prepararlo. La tematica principale di questo talk è **l'algoritmo di Pohlig-Hellman (1978)** per risolvere, sotto determinate ipotesi, il problema del logaritmo discreto in tempi utili. Questo algoritmo è strettamente connesso allo scambio di chiavi di Diffie-Hellman, che basa la propria solidità sulla difficoltà computazionale del problema del logaritmo discreto. Per comprimere tutto in 30 minuti, e non rendere il talk troppo pesante, ho dovuto rinunciare a molte dimostrazioni, spero di avere il tempo, prima o poi, di riscrivere queste slide sotto forma di documento PDF in LaTex. Ho basato questo talk principalmente su un magnifico documento PDF presente nel sito del **NTNU**, Norwegian University of Science and Technology, ma non ho trovato il nome dell'autore (purtroppo). Lo reputo davvero un bel documento, che spiega in maniera semplice e rigorosa il protocollo dello scambio di chiavi di Diffie-Hellman e l'algoritmo di Pohlig-Hellman. Anche alla fine di queste slide è presente un QR code che riporta la bibliografia.

{{< youtube RKAZWdLbxZQ >}}
&nbsp;

  * [Diffie-Hellman and Discrete Logarithms [Download PDF]][4]
  * [IHC 2018 &#8211; The Diffie-Hellman protocol is weak for Fermat primes [Download PDF]][5]

 [1]: https://www.ihc.camp/
 [2]: https://www.ihc.camp/codice-di-condotta/
 [3]: /docs/2018/08/IHC2018-Image-sharing-sites-and-stored-XSS.pdf
 [4]: https://wiki.math.ntnu.no/_media/tma4160/2015h/dh.pdf
 [5]: /docs/2018/08/IHC-2018-The-Diffie-Hellman-protocol-is-weak-for-Fermat-primes.pdf