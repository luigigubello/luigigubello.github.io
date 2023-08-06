---
title: Utilizzare il Raspberry Pi 3 via Termux
author: Luigi Gubello
type: post
date: 2018-03-07T09:08:20+00:00
url: /blog/utilizzare-il-raspberry-pi-3-via-termux/
categories:
  - Italian
  - Raspberry

---

Qualche mese fa ho comprato un Raspberry Pi 3 Model B, l'ho comprato per curiosità, per giocarci (letteralmente). Nelle settimane successive ho acquistato un piccolo [schermo da 3,5 pollici][2], una powerbank e un paio di joystick (modello Nintendo SNES) e grazie al bellissimo progetto [RetroPie][3] ho trasformato il mio Raspberry in una console portatile di giochi retro.

L'obiettivo di questo post è spiegare come configurare un Raspberry per poterlo usare tramite smartphone Android via connessione SSH, anche se il Raspberry non è connesso a nessuna rete wi-fi.

Io mi sono trovato, casualmente, a dovermi arrangiare con questi oggetti:

  * Raspberry Pi 3 Model B + Raspbian Stretch
  * TP Link TL-WN722N v3

La mia chiavetta ha un chipset Realtek e Raspbian Stretch la riconosce subito automaticamente, ma se così non fosse basta dare i seguenti comandi per risolvere la situazione:

```
sudo apt-get update
sudo apt-get install firmware-realtek
```

Per utilizzare il Raspberry via SSH anche se quest'ultimo non è connesso a una rete l'idea è quella di usare (almeno) due antenne wi-fi, una per creare un access point e  un'altra libera di connettersi a internet. Una volta creato un AP potremmo connetterci via SSH tramite l'applicazione [**Termux**][5] e utilizzare così il terminale. In ordine ho dovuto:

  1. Installare _Network Manager_ e rinunciare a _dhcpd_
  2. Configurare Raspbian affinché ad ogni avvio assegni sempre gli stessi nomi alle antenne wi-fi
  3. Creare un access point e far sì che questo si crei in automatico ad ogni avvio

### 1.

Ho deciso di rinunciare a **dhcpd**_ _in favore di **Network Manager**, perché quest'ultimo semplifica molto la vita quando ci si vuole connettere a una rete wi-fi usando solo il terminale e perché con dhcpd, usando l'antenna integrata, Raspbian Stretch mi si blocca in maniera quasi sistematica. Seguendo un [commento][6] su StackExchange, do i seguenti comandi nel terminale:

```
sudo apt-get install network-manager network-manager-gnome
sudo apt-get purge openresolv dhcpcd5
sudo ln -sf /lib/systemd/resolv.conf /etc/resolv.conf
```

Fatto ciò posso rimuovere l'icona del wi-fi nel pannello in alto, che verrà sostituita dall'icona di Network Manager.

`reboot`

### 2.

A questo punto devo assegnare dei nomi fissi alle due antenne wi-fi, quella interna e quella esterna. Dopo numerosi tentativi falliti scopro, grazie a un [topic][7] sul forum di RaspberryPi, che prima bisogna eliminare il file **/etc/systemd/network/99-default.link**.  
Ritento e fallisco ancora, scopro infatti che [Network Manager cambia i MAC Address ad ogni avvio][8], per evitare ciò modifico il file **/etc/NetworkManager/NetworkManager.conf** e aggiungo le seguenti righe:

```
[device]
wifi.scan-rand-mac-address=no

[connection]
ethernet.cloned-mac-address=preserve
wifi.cloned-mac-address=preserve
```

Salvo e riavvio due volte per accertarmi che i MAC Address siano persistenti. Una volta fatto ciò mi basta creare il file **/etc/udev/rules.d/70-persistent-net.rules **e aggiungerci il seguente testo:

```
ACTION=="add", SUBSYSTEM=="net", DRIVERS=="brcmfmac", NAME="wlan0"
ACTION=="add", SUBSYSTEM=="net", DRIVERS=="?*", ATTR{address}=="mac:addr:ext:wifi", NAME="wlan1"
```

Riavvio, per accertarmi che i nomi siano correttamente assegnati: **wlan0** è l'antenna integrata nel Raspberry e servirà a creare l'access point e **wlan1 **è l'antenna USB che servirà per collegarsi alle reti wi-fi .

### 3.

Ora bisogna creare un access point, ci sono diverse guide in internet, ma io ho trovato molto comodo e veloce il programma [**create_ap** di **oblique**][9]. Procedo quindi all’installazione di **create_ap**:

```
git clone https://github.com/oblique/create_ap
cd create_ap
sudo make install
```

Digito ora i seguenti comandi, che servono a creare un access point con l'antenna integrata del Raspberry, appoggiarsi all'antenna esterna per collegarsi ad una rete wi-fi e ripetere quest'azione ad ogni avvio:

```
sudo create_ap wlan0 wlan1 AccessPoint PasswordAP --mkconfig /etc/create_ap.conf
sudo systemctl start create_ap
sudo systemctl enable create_ap
reboot
```

A questo punto, se tutto fila liscio, appare la rete wi-fi _AccessPoint_, collego il telefono a questa rete wi-fi, e apro Termux dove digito il comando `ssh root@192.168.12.1`. Inserisco le credenziali d'accesso et voilà! Posso utilizzare il Raspberry comodamente tramite smartphone. Un programma utile per gestire al meglio le connessioni SSH è **screen**.

C'è un ultimo problema da risolvere: l'access point creato per ora non ha una connessione ad internet, e se il telefono è connesso alla rete creata dal Raspberry allora non può essere connesso a un'altra rete wi-fi (e quindi è privo di una connessione internet). Basta utilizzare quindi **wlan1** per connettere il Raspberry ad una rete wi-fi funzionante. Per fare ciò uso il comando **nmcli**, che non è altro che il Network Manager da riga di comando.

```
nmcli device wifi rescan
nmcli device wifi list
nmcli device wifi connect SSID-Name password wireless-password
```

Ringrazio [un post di][10] [Peter Robinson][11] per avermi fatto scoprire questo comando e avermi semplificato la vita. Questa, per ora, è la soluzione che ho adottato io.

**Pareri e conclusioni personali:**

  * Kali 2017.3 è una buona distribuzione per Raspberry, soprattutto grazie al suo kernel e ai repository molto ampi, ma non è pensata per un utilizzo giornaliero.
  * La mia esperienza con Raspbian Jessie è stata di gran lunga migliore di quella con Raspbian Stretch.
  * Il progetto Retropie per emulare vecchi videogiochi è davvero bellissimo, se non sapete come utilizzare un Raspberry, compratevi due joystick, installate Retropie e giocate a Mario Kart, che è avvincente anche dopo trent'anni.
  * Ho provato Ubuntu Mate 16 e personalmente l'ho trovato terribile.

 [2]: http://www.quimat.cn/cpzs/sjpj/313.html
 [3]: https://www.retropie.it/
 [4]: https://whitedome.com.au/re4son/re4son-kernel/
 [5]: https://termux.com/
 [6]: https://raspberrypi.stackexchange.com/questions/29783/how-to-setup-network-manager-on-raspbian
 [7]: https://www.raspberrypi.org/forums/viewtopic.php?t=191639
 [8]: https://forums.kali.org/showthread.php?33405-SOLVED-NetworkManager-reverts-spoofed-MAC-to-permanent-one-or-randomizes-it
 [9]: https://github.com/oblique/create_ap
 [10]: https://nullr0ute.com/2016/09/connect-to-a-wireless-network-using-command-line-nmcli/
 [11]: https://nullr0ute.com/about/