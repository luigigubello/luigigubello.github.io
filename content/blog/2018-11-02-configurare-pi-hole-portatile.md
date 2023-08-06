---
title: Configurare un Pi-Hole portatile
author: Luigi Gubello
type: post
date: 2018-11-02T12:02:07+00:00
url: /blog/configurare-pi-hole-portatile/
categories:
  - Italian
  - Raspberry

---
Poco tempo fa ho scoperto un progetto open source chiamato **Pi-Hole**, piuttosto conosciuto e apprezzato. Il compito di Pi-Hole è quello di "ripulire" la nostra navigazione su Internet da pubblicità e siti malevoli (semplificando: funge da ad-block), creando un piccolo server DNS sul Raspberry. Uno dei principali punti di forza è che, una volta configurato, ripulisce dalle pubblicità ogni dispositivo connesso alla rete, senza bisogno di ulteriori programmi o plug-in.

Questo progetto mi ha entusiasmato così tanto (mi ha tolto la pubblicità invasiva e fastidiosa anche da tutte le applicazioni installate su Android) che ho deciso di provare a configurarlo in modo da portarlo facilmente in giro, senza bisogno di cavi ethernet o configurazioni particolari del router. Avendo anche un piccolo schermino da 3,5 pollici per il Raspberry l'ho configurato in modo tale che sul monitor appaiano alcune informazioni utili, ma questa è un'aggiunta superflua. Per configurare Pi-Hole come volevo ho usato:

  * Raspberry Pi3 Model B + Raspbian Stretch Lite (2018-10-09)
  * TP Link TL-WN722N v3
  * [Quimat LCD Display da 3,5 pollici][1] **(opzionale)**

### 1.

Lo schermino LCD Quimat è opzionale, nel caso non si disponga di questo pezzo basta munirsi di classico cavo HDMI e monitor e saltare la parte della guida riguardante l'installazione dei driver per il display. La versione Lite di Raspbian si usa solo da linea di comando, quindi può essere utile avere una tastiera a portata di mano. Per prima cosa prepariamo una scheda SD estraendoci dentro Raspbian Lite, per fare ciò Balena Etcher (ex Etcher) andrà benissimo. A fine processo la memoria SD sarà divisa in due partizioni: **boot** e **rootfs**. Nella partizione **boot** creiamo due files: uno chiamato **ssh**, vuoto, e uno chiamato **wpa_supplicant.conf**. All'interno di quest'ultimo incollate il seguente testo, inserendo il nome della vostra connessione wi-fi e la password: 

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US 

network={
  ssid="Your-WiFi-SSID"
  psk="Your-WiFi-Password"
  scan_ssid=1
}
```

Questi due files ci serviranno per connetterci via SSH al Raspberry fin dal primo avvio. [Scarichiamo i driver][2] per il display Quimat, estraiamoli nella cartella _LCD-show_, e copiamo la cartella nella partizione **rootfs**, nel percorso **/home/pi**. Montiamo il display sul Raspberry e accendiamolo. Collegandoci via SSH diamo i seguenti comandi:  

```
chmod -R 755 LCD-show
cd LCD-show
sudo ./LCD35-show
```

Il Raspberry dovrebbe riavviarsi e sul display Quimat dovrebbe finalmente apparire la shell. Ora bisogna creare "ponte" tra l'antenna integrata del Raspberry (_wlan0_) e l'antenna WiFi USB (_wlan1_), per fare ciò basta seguire [questa][3] mia vecchia guida, ma utilizzando questa volta Raspbian Lite possiamo rinunciare all'installazione del pacchetto _network-manager-gnome_. La configurazione finale di Raspbian tramite **raspi-config** è lasciata all'utente, in base alle proprie abitudini e/o esigenze.

### 2.

Ora è possibile installare Pi-Hole, diamo il seguente comando: `curl -sSL https://install.pi-hole.net | bash`

L'installazione è guidata e piuttosto facile, almeno per il momento conviene tenere le impostazioni di default, tranne per due configurazioni: quanto richiede l'interfaccia selezioniamo **wlan0** e quando ci chiede quali IP utilizzare nella riga _IP Address_ scriviamo **192.168.12.1/24** e nella riga _Gateway_ scriviamo **192.168.12.1**. Concludiamo l'installazione e diamo infine il comando: `sudo apt-get purge dhcpcd5`.  
Quest'ultimo pacchetto viene installato in automatico da Pi-Hole ad ogni riconfigurazione che andremo a fare, e bisogno ricordarsi di toglierlo alla fine perché entra in conflitto con il pacchetto _network-manager_.

### 3.

**Se non stai utilizzando uno schermo LCD puoi saltare questa parte.** Per sfruttare al meglio il piccolo display Quimat ho deciso di usarlo in combinazione con [PADD][4], un piccolo script che dà informazioni riguardo a Pi-Hole, che ho scoperto leggendo una [guida][5] sul sito di Adafruit. Scarichiamo lo script: `wget -N https://raw.githubusercontent.com/jpmck/PADD/master/padd.sh`. Ora con **nano** andiamo a commentare la seguente riga:  

```
# export LC_ALL=en_US.UTF-8 > /dev/null 2>&1 || export LC_ALL=en_GB.UTF-8 > /dev/null 2>&1 || export LC_ALL=C.UTF-8 > /dev/null 2>&1
```

Per far avviare PADD ad ogni avvio di Raspbian modifichiamo il file **.bashrc** aggiungendo alla fine le seguenti righe:  

```
# Run PADD
if [ "$TERM" == "linux" ] ; then
  while :
  do
    ./padd.sh
    sleep 1
  done
fi
```

E infine rendiamo eseguibile lo script: `chmod +x padd.sh`. Ora al prossimo riavvio PADD viene lanciato in automatico mostrando le informazioni relative a Pi-Hole.

### 4.

L'ultima parte riguarda la messa in sicurezza di Pi-Hole. L'idea è quella di poter portare il Raspberry facilmente in giro e di poter usufruire di Pi-Hole anche su reti che non sono la nostra, come ad esempio il wi-fi di un albergo. Per prima cosa quindi bisogna cambiare la password di Raspbian e quella del pannello di controllo di Pi-Hole (_pi.hole/admin/_). Il Raspberry ora dovrebbe funzionare più o meno così:

`[device]–>[raspberry][wlan0]–>[wlan1][/raspberry]–>[router]`

Pi-Hole è impostato su _wlan0_, che fa da hotspot, e _wlan1_ viene utilizzata per connettersi ai vari router. Quindi bisogna fare in modo che:

  1. I dispositivi che si connettono a _wlan0_ non abbiano accesso non autorizzatto alla pagina _pi.hole/admin/_.
  2. Il Raspberry non sia esposto a particolari rischi quando si connette a un router tramite _wlan1_.

Configuriamo quindi **lighttpd** in modo tale che vengano richiesti _user_ e _password_ per accedere a _pi.hole/admin/_ e che l'accesso a questa pagina sia ristretto solo a determinati IP (cioè solo agli utenti connessi a _wlan0_). Nella cartella **/etc/lighttpd** modifichiamo (o creiamo, nel caso non esistesse) il file **external.conf**, aggiungendo queste righe:  

```
 $HTTP["remoteip"] != "192.168.12.1/24" {
  url.access-deny = ( "" )
}
```

Ora controlliamo che nel file **lighttpd.conf** siano presente le seguenti righe, altrimenti bisogna aggiungerle alla fine del file:  

```
 # Add user chosen options held in external file
include_shell "cat external.conf 2>/dev/null"
```

A questo punto solo gli IP **192.168.12.\*** avranno accesso alla pagina _pi.hole_. Ora aggiungiamo una richiesta di autenticazione, per fare ciò ho seguito una [semplice guida][6] di [Jacob Salmela][7]. Per prima cosa creiamo in _/etc/lighttpd/_ una cartella chiamata **.htpasswd**: `sudo mkdir /etc/lighttpd/.htpasswd`. All'interno di questa cartella creiamo lo script **hash.sh** contenente il seguente codice:  

```
 #!/bin/sh
user=$1
realm=$2
pass=$3
hash=`echo -n "$user:$realm:$pass" | md5sum | cut -b -32`
echo "$user:$realm:$hash"
```

Rendiamo eseguibile lo script e lanciamolo, scegliendo un nome utente e una password al posto di **user** e **password**.  

```
sudo chmod +x hash.sh
sudo ./hash.sh 'user' 'pihole' 'password'
```

Lo script restituirà una stringa come output, copiamola e incolliamola in un nuovo file chiamato **lighttpd-htdigest.user**, che va salvato sempre nella cartella **./htpasswd**. Infine apriamo nuovamente _lighttpd.conf_ e incolliamo il seguente testo:  

```
 # Mod_Auth Configuration
auth.backend = "htdigest"
auth.backend.htdigest.userfile = "/etc/lighttpd/.htpasswd/lighttpd-htdigest.user"
auth.require = ( "/admin/" =>
    (
    "method" => "digest",
    "realm" => "pihole",
    "require" => "user=user"
    ),
)
```

Al posto di **user** bisogna inserire il nome utente usato nel file **lighttpd-htdigest.user**, salviamo il file e riavviamo lighttpd: `sudo service lighttpd restart`. Ora per accedere alla pagina _pi.hole_ dovrebbero venirci richiesti user e password. Se tutto è andato per il verso giusto , occupiamoci di restringere l'accesso al protocollo SSH. Apriamo il file **/etc/ssh/sshd_config** e aggiungiamo le seguenti righe:  

```
 # Block all users except from 192.168.12.*
AllowUsers *@192.168.12.*
```

Così facendo solo gli utenti con IP **192.168.12.\*** (cioè quelli connessi a _wlan0_) potranno connettersi via SSH al Raspberry.  
Adesso bisogna bloccare in entrata le porte **22** e **53** dell'antenna _wlan1_, che è quella che espone il Raspberry a potenziali minacce collegandosi a router estranei. Dirigiamoci nella directory **/home/pi/** e digitiamo i seguenti comandi:  

```
sudo apt-get install iptables-persistent
mkdir iptables && cd iptables
sudo iptables-save > /home/pi/iptables/iptables
cp iptables iptables.orig
```

Ora apriamo con _nano_ il file _iptables_ e aggiungiamo le seguenti regole per rifiutare il traffico in entrata sulle porte 22 e 53 su _wlan1_:  

```
 -A INPUT -i wlan1 -p tcp --destination-port 22 -j REJECT
-A INPUT -i wlan1 -p tcp --destination-port 53 -j REJECT
```

Ora importiamo le nuove regole e impostiamo che vengano usate ad ogni avvio, diamo quindi i seguenti comandi:  

```
sudo iptables-restore /home/pi/iptables/iptables
cd /etc/network/if-pre-up.d
sudo nano iptables
```

All'interno di questo nuovo file _iptables_ inseriamo il seguente codice (grazie [Debian Wiki][8] ❤):  

```
#!/bin/sh
/sbin/iptables-restore < /home/pi/iptables/iptables
```

Per ultimare rendiamo eseguibile lo script e riavviamo: `sudo chmod +x iptables && sudo reboot`. Dopo il riavvio colleghiamo il Raspberry, tramite comando **nmcli**, al router di casa (o a un router qualsiasi in realtà) e controlliamo che le porte 22 e 53 non siano visibili (per farlo basta collegare un telefono Android al medesimo router e con _Termux_ dare il comando `nmap <em><ip_raspberry></em>`).

A questo punto la configurazione è praticamente ultimata, ma per scrupolo andremo a creare un **certificato SSL** per la pagina web _pi.hole_. Essendo la pagina _pi.hole_ raggiungibile solamente se si è collegati a _wlan0_ ho deciso che un certificato autofirmato è sufficiente, visto che siamo noi stessi a crearlo possiamo reputarlo attendibile. Chrome e Firefox (e presumo ogni altro browser) non lo reputeranno tale perché noteranno che è autofirmato e non è certificato da nessun ente terzo, questo non è un problema, come viene spiegato anche nella [pagina di supporto di Mozilla][9], e basta aggiungerlo tra le eccezioni. Dirigiamoci nella directory **/etc/lighttpd** e diamo i seguenti comandi:  

```
sudo mkdir certs && cd certs
sudo openssl req -new -x509 -keyout pihole.pem -out pihole.pem -days 365 -nodes
```

Potete lasciare vuoti tutti i campi eccetto quello chiamato **Common Name** in cui dovrete inserire **pi.hole**, infine date il seguente comando: `sudo chmod 400 pihole.pem`. Ora torniamo nella cartella**/etc/lighttpd** e modifichiamo il file **lighttpd.conf** aggiungendo il seguente testo:  

```
# Enable certificate SSL
$SERVER["socket"] == ":443" {
  ssl.engine = "enable"
  ssl.pemfile = "/etc/lighttpd/certs/pihole.pem"
}

# Redirect HTTP to HTTPS
$HTTP["scheme"] == "http" {
  $HTTP["host"] =~ ".*" {
    url.redirect = (".*" => "https://%0$0")
  }
}
```

Perfetto, ora riavviamo _lighttpd_ come fatto in precedenza. A questo punto tutto dovrebbe essere filato per il verso giusto, quindi facciamo un backup di alcuni files:  

```
sudo cp /etc/lighttpd/lighttpd.conf /etc/lighttpd/lighttpd.conf.bk
cp /home/pi/iptables/iptables /home/pi/iptables/iptables.bk
```

E riavviamo. _That’s all folks!_

Lascio un QR code contenente tutti i post, le discussioni, le guide e le varie letture che mi sono tornate utili per configurare il tutto.

![QR Code](/images/2018/11/pihole_bibliografia.png#center)

 [1]: https://www.amazon.it/Raspberry-Quimat-Resolution-Protective-QSC11/dp/B06W55HBTX/ref=pd_sbs_147_2?_encoding=UTF8&pd_rd_i=B06W55HBTX&pd_rd_r=a0b0b9ae-de01-11e8-b659-c3ef5a38f9fa&pd_rd_w=gR58Z&pd_rd_wg=R8fgN&pf_rd_i=desktop-dp-sims&pf_rd_m=A11IL2PNWYJU7H&pf_rd_p=466c5af4-0171-4b17-9b3f-b4036a90f75d&pf_rd_r=3KT6KMEV303JBPPMNGBV&pf_rd_s=desktop-dp-sims&pf_rd_t=40701&psc=1&refRID=3KT6KMEV303JBPPMNGBV
 [2]: https://github.com/goodtft/LCD-show
 [3]: http://localhost/blog/utilizzare-il-raspberry-pi-3-via-termux/
 [4]: https://github.com/jpmck/PADD
 [5]: https://learn.adafruit.com/pi-hole-ad-pitft-tft-detection-display/install-padd
 [6]: https://jacobsalmela.com/2014/05/25/password-protect-a-lighttpd-web-server-on-a-raspberry-pi-using-mod-auth/
 [7]: https://jacobsalmela.com
 [8]: https://wiki.debian.org/it/iptables
 [9]: https://support.mozilla.org/it/kb/questa-connessione-non-sicura#w_il-certificato-non-ae-affidabile-in-quanto-auto-firmato