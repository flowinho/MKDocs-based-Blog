---
date: 2023-05-24
categories:
    - Server
    - Ubuntu
tags:
    - ubuntu
    - vps
    - ufw
    - ssh
    - domain
---

# Initiale Einrichtung eines Ubuntu 22.04 VPS

Einen VPS zu besitzen ist toll, aber auch ein großes Risiko. Hier ein paar Tipps zur initialen Einrichtung eines neu erworbenen VPS. Und während ich natürlich keinerlei Garantie dafür übernehme dass der eingerichtete Server sicher ist, halte ich die Einstellungen zumindest für "reasonably secure".

<!-- more -->

Folgende Dinge sollten vorbereitet werden:

- SSH Key für Geräte vorbereiten, zB. für Hauptrechner, für iOS Devices, für weitere Geräte, alle auf die Maschine kopieren die zum Einrichten genutzt wird.
- Stabile Internet-Verbindung
- Optional: Domain auf den VPS zeigen lassen. Dazu einen `A`-Eintrag anlegen, der auf die IP des Servers zeigt.

!!! danger "Minimale Konfiguration"

    Die hier beschriebene Konfiguration kann nur als minimale Konfiguration zur initialen Absicherung eines VPS angesehen werden. Weitere Schritte sind notwendig, um einen Production-Server korrekt abzusichern.

## Login als root und Systemaktualisierung ausführen

```bash
ssh root@<server-ip> # Initiale Verbindung zum Server
apt update && apt upgrade -y # Paketquellen aktualisieren und alle Aktualisierungen einspielen.
```

## Neuen Non-Root User anlegen

```bash
adduser username # Neuen User hinzufügen
usermod -aG sudo username # Neuem User sudo Rechte geben
```

## Basis-Konfiguration Firewall

```bash
ufw app list # Ist OpenSSH vorhanden?
ufw allow OpenSSH # Port 22 IPv4 und IPv6 automatisch freigeben
ufw enable # UFW aktivieren
```

## SSH Konfiguration

```bash
ssh-copy-id -i ~/.ssh/<key> <user>@<host> # Kopieren eines oder mehrere PublicKeys auf den Server
# Für jeden Key wiederholen
# ----- Änderung der Config
sudo vim /etc/ssh/sshd_config # Änderung der Config
Port <port> # Port weg vom Standard ändern
PasswordAuthentication no # PasswortLogin deaktivieren
PubkeyAuthentication yes # Public Key Authentication explizit aktivieren
PermitRootLogin no # Login als Root generell nicht erlauben
AllowUsers <user> # Erlaubt explizit nur dem erstellen User den Zugriff auf den Server via SSH
# ---- SSH Service neu starten
sudo systemctl restart ssh
sudo ufw allow <port> # WICHTIG, der geänderte Port muss der Firewall hinzugefügt werden!!
sudo ufw limit <port>/tcp comment 'SSH port rate limit' # Rate Limit, um Missbrauch etwas vorzubeugen.
```

**Optional:** Name der Maschine ändern

```bash
sudo vim /etc/hostname # Hostnamen in neuen Namen ändern
sudo vim /etc/hosts # Alle Erwähnungen des alten Hostnamen durch den Neuen ersetzen.
```

**Optional, aber cool:** motd - Message of the Day ändern

```bash
sudo vim /etc/motd # Inhalt ändern
```

Befehl, um sich mit der auf diese Weise eingerichteten Maschine zu verbinden:

```bash
ssh <user>@<host> -p <post> -i ~/.ssh/<key>  
```
