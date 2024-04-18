# SSH Keys

SSH Keys sind sehr praktisch um sich ohne Benutzername & Passwort irgendwo zu authentifizieren, beispielsweise bei GitHub oder einem Virtual Private Server. ED25519 gilt als sicherer als RSA, insbesondere im Bezug auf Quantum Computing.

Key generieren

```bash
ssh-keygen -o -a 256 -t ed25519 -C "$(hostname)-$(date +'%d-%m-%Y')"
```

Key kopieren

```bash
cat ~/.ssh/id_ed25519.pub | pbcopy
```
