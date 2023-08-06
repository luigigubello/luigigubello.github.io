---
title: Quali utenti usano 🇮🇹 nel nickname di Twitter?
author: Luigi Gubello
type: post
date: 2018-07-19T10:40:28+00:00
url: /blog/quali-utenti-usano-bandiera-ita-nel-nickname-di-twitter/
categories:
  - GitHub
  - Italian
  - Other
draft: true
---
Qualche giorno fa il giornalista de Il Post Emanuele Menietti ha tweetato questo:

{{< tweet user="emenietti" id="1015510268393254912" >}}

Mi sono quindi domandato se c'è un modo veloce per filtrare gli utenti di Twitter in base alla presenza, o meno, di una determinata emoji nel loro nickname. La risposta è **Sì**, bastano qualche riga di Python e l'utilizzo delle API di Twitter. A questo punto ho deciso di giocare un po' di più con Python e le API di Twitter, provando a vedere se era possibile rispondere a qualche domanda, utilizzando i dati ottenibili tramite le API. Le domande che mi sono posto sono principalmente due:

  * In che percentuale gli utenti con 🇮🇹 nel nickname, ma che **non **hanno le seguenti emoji 🇪🇺 / 🏳️‍🌈 / 🌈 nel nickname, seguono _attivamente_ i principali politici italiani?
  * Viceversa, in che percentuale gli utenti con 🇪🇺 / 🏳️‍🌈 / 🌈 nel nickname seguono _attivamente_ i principali politici italiani?

Chiarisco una questione: questa **non** è un'analisi scientifica. È solo un esercizio, l'utilizzo delle API di Twitter tramite il linguaggio Python. Twitter impone delle limitazioni all'utilizzo delle API nella modalità Free, ad esempio limitando fortemente il numero di dati che si possono ottenere, ma permette di utilizzarli, seppur in maniera limitata, e questo basta per capire come funzionano e per giocarci un po'. Tornando alle due domande poste sopra, per "attivamente" intendo account Twitter che interagiscono, quindi mi sono interessato a tutti quegli utenti che interagiscono retweetando un tweet di un determinato politico. Per "principali politici" invece ho deciso di prendere in considerazione i leader dei principali partiti che erano presenti alle elezioni del 4 marzo, quindi: Pietro Grasso, Emma Bonino, Matteo Renzi, Luigi Di Maio, Silvio Berlusconi, Matteo Salvini e Giorgia Meloni. A questi nomi ho aggiunto anche Beppe Grillo, in quanto fondatore e garante del Movimento 5 Stelle, e Paolo Gentiloni, in quanto Presidente del Consiglio in carica prima delle elezioni del 4 marzo.

A questo punto è bastato scrivere un codice Python che, tramite le API di  Twitter, facesse questo:

  * Creare una lista con gli ultimi 25 tweet postati da un account, escluse le risposte e i retweet
  * Per ogni tweet presente nella lista, selezionare al più 100 utenti che hanno retweetato il tweet e controllare se hanno determinate emoji nel nickname
  * Tenere conto del numero di utenti con determinate emoji nel nickname per poi riportare i dati in un grafico

Il limite di "al più 100 utenti che hanno retweetato" è uno di quei limiti imposti da Twitter nell'utilizzo gratuito delle API. Tra l'altro non seleziona mai cento utenti, ma per qualche motivo si limita a circa una novantina (mi sono domandato il perché, ma non ho approfondito per ora). Il codice che ho scritto lo potete trovare su [GitHub][1].

I risultati sono interessanti: gli utenti che hanno 🇮🇹 nel nickname, ma che **non** hanno 🇪🇺 / 🏳️‍🌈 / 🌈 nel nickname, interagiscono (tramite retweet) principalmente con gli account di Matteo Salvini, Giorgia Meloni e Luigi Di Maio. Per essere più precisi, tra gli utenti che hanno retweetato gli ultimi 25 tweet di Matteo Salvini mediamente il **15,70%** aveva l'emoji 🇮🇹 nel nickname. Per Giorgia Meloni la percentuale è invece del **12,58%** e per Luigi Di Maio è del **7,33%**.  
Questi tre profili sono anche quelli con cui gli utenti che hanno almeno una tra le emoji 🇪🇺 / 🏳️‍🌈 / 🌈 nel nickname interagiscono di meno: per Matteo Salvini la percentuale è **0,20%**, per Giorgia Meloni è **0,12%** e per Luigi Di Maio è **0,16%**.

Gli utenti con 🇪🇺 / 🏳️‍🌈 / 🌈 nel nickname interagiscono prevalentemente con gli account di Emma Bonino, Paolo Gentiloni e Matteo Renzi, anche se con percentuali minori se confrontate agli utenti con l'emoji 🇮🇹 che interagiscono con gli altri tre leader. Emma Bonino ottiene una percentuale di **9,41%**, mentre Paolo Gentiloni e Matteo Renzi ottengono una percentuale di **5,29%** e **4,91%** rispettivamente. In maniera antisimmetrica, gli utenti con 🇮🇹 nel nickname non sembrano interagire (tramite retweet) con questi tre leader: infatti per tutti e tre la percentuale è inferiore a **1%**.

L'account di Beppe Grillo ottiene percentuali simili a quelle di Matteo Renzi, ma ad utenti invertiti: gli utenti che hanno 🇮🇹 nel nickname sono mediamente il **4,12%** di quelli che hanno retweetato i tweet di Beppe Grillo, mentre gli utenti con le emoji 🇪🇺 / 🏳️‍🌈 / 🌈 sono mediamente un irrisorio **0,04%**, praticamente assenti. Per quanto riguarda gli account di Silvio Berlusconi e Pietro Grasso, entrambi i gruppi di utenti sembrano interagire poco con questi account.

Un riassunto dei risultati, con i relativi grafici, lo potete trovare in questo [file pdf][2]. Ci tengo ora ad elencare i limiti di questa "analisi":

  * Le percentuali sono solo proiezioni ottenute da un campione piuttosto piccolo
  * Non sono stati presi in considerazione il numero di followers e la viralità dei tweet analizzati
  * I 25 tweet presi come campione coprono un arco temporale differente per ogni account
  * Non è stato fatto nessun controllo sugli account che retweetavano, per capire ad esempio se si trattava di account poco attendibili
  * Le API gratuite di Twitter limitano fortemente la quantità di dati analizzabile
  * I risultati riguardano solo gli account analizzati, e non si possono generalizzare

Nonostante i limiti del codice da me scritto e delle API di Twitter, sugli account presi in considerazione sembra crearsi una polarizzazione: se escludiamo gli account di Silvio Berlusconi e Pietro Grasso, che hanno basse percentuali per entrambi i gruppi di persone, per gli altri account _sembra_ delinearsi il fatto che la presenza di un gruppo esclude la presenza dell'altro.

I dati sono stati calcolati tutti nella notte del 19 luglio 2018, nella speranza di rendere un po' omogenea questa "analisi". È facile che queste percentuali mutino velocemente nel tempo, per diversi fattori.

P.S. So che il titolo non è dei migliori, ma non ho saputo fare di meglio.

 [1]: https://github.com/luigigubello/analisi_emoji/blob/master/analisi_bandiera.py
 [2]: https://github.com/luigigubello/analisi_emoji/blob/master/analisi.pdf