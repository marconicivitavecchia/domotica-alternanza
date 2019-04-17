# Alternanza Domotica

Benvenuti al corso di domotica del Marconi!!

## Cosa serve per cominciare
- iscriversi al canale Telegram del Marconi (chiedere ad un docente per l'accesso)
- ogni partecipante deve avere un account su [GitHub](https://github.com)
- una scheda SD da minimo 8GB (massimo 32GB), categoria 10

## Preparazione della macchina di sviluppo
Installare nella propria macchina Windows:
- [Git Bash](https://git-scm.com/downloads)
- [WinSCP](https://winscp.net/eng/download.php)
- [Visual Studio Code](https://code.visualstudio.com/)
- [BalenaEtcher](https://etcher.io)

## Configurazione della Raspberry
Per cominciare, configuriamo le Raspberry in modo da poterla trasformare nel nostro server per la domotica. Seguiremo [questo](https://desertbot.io/blog/headless-raspberry-pi-3-bplus-ssh-wifi-setup) tutorial per configurare la Raspberry senza dover collegare né lo schermo, né un cavetto seriale.

Prima di tutto scaricate [Raspbian Stretch Lite](https://www.raspberrypi.org/downloads/raspbian/) dal sito ufficiale e installatela sulla scheda SD usando BalenaEtcher che avete scaricato all'inizio.

Ricordatevi che di default la Raspberry usa i seguenti parametri:
- login: pi 
- psw: raspberry

La procedura di configurazione è la seguente.
1. attivare il server SSH creando un file vuoto `/boot/ssh`, fare attenzione che il file sia completamente vuoto e che il terminatore di riga sia impostato su `LF`

2. attivare la UART USB in `/boot/config.txt`
```sh
#enableUART
enable_uart=1 
```
3. configurare la rete `marconiopen` della scuola con autenticazione WPA/WPA2 creando il file `/boot/wpa_supplicant.conf`
```conf
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=IT

network={
    ssid="marconiopen"
    psk="marconiopen"
    key_mgmt=WPA-PSK
}
```

Opzionale: se volete configurare anche una rete WiFi di casa vostra, potete usare il seguente file `/boot/wpa_supplicant.conf`:
```conf
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=IT

network={
    ssid="marconiopen"
    psk="marconiopen"
    key_mgmt=WPA-PSK
	priority=1
}

network={
    ssid="ssid-casa"
    psk="psk-casa"
    key_mgmt=WPA-PSK
	priority=2
}
```
4. configurare le interfacce di rete nel file `/etc/network/interfaces`, sostituendo il contenuto del file con lo snippet seguente
```sh
# interfaces(5) file used by ifup(8) and ifdown(8)
# Please note that this file is written to be used with dhcpcd 
# For static IP, consult /etc/dhcpcd.conf and 'man dhcpcd.conf'

# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d 
auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```

5. abilitare l'account di root.
```sh
sudo nano /etc/ssh/sshd_config
PermitRootLogin without-password  -->  PermitRootLogin yes
/etc/init.d/ssh restart
sudo passwd root
```
**Disclaimer**: abilitare l'account di root solitamente è una cosa molto sconsigliata. Noi l'usiamo per comodità per copiare alcuni file con WinSCP. Se volete di nuovo bloccare l'account di root, usate il seguente comando:
```sh
sudo passwd -l root
```

Bene, ora la vostra Raspberry è configurata, complimenti! 

Per ulteriori dettagli e procedure aggiuntive, rimandiamo alle guide complete del prof. Melita:
- [Flashare la RaspBerry - Istruzioni.pdf](resource/Flashare-la-RaspBerry-Istruzioni.pdf)
- [raspbianConfig.txt](resource/raspianConfig.txt)

### Altre risorse utili
- [Raspberry pinout](resource/raspbery-pi-3-gpio-pinout-40-pin-header-block-connector-1-1.png)
- Cavo USB-TTL (per collegamento seriale - UART): [connessione](resource/USBTTLPinout.png), [pinout](resource/usbttlpinout.jpg), [acquisto](https://www.amazon.it/Sumind-Raspberry-Programmazione-Seriale-Supported/dp/B01N4X3BJB/ref=sr_1_fkmr2_1?ie=UTF8&qid=1553507750&sr=8-1-fkmr2&keywords=Usb+To+TTL+Pl2303ta)

- Domotica for Dummies: [parte 0](resource/domotica4dummies/domotica4dummies_parte-0.pdf), [parte 1](resource/domotica4dummies/domotica4dummies_parte-1.pdf)

