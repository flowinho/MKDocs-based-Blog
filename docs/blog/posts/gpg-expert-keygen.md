---
date: 2023-05-25
categories:
- Kryptografie
tags:
- gpg
- rsa
- ecdsa
- gnupg
---

# GPG Expert Keygen: Keys auf die richtige Weise generieren

GPG Keys sind der wahrhaftige "Schlüssel" zur Identität. Mit ihnen können Commits auf GitHub unterschrieben, Emails und Dateien verschlüsselt werden, oder Zugänge zu Servern oder Diensten abgesichert werden. Die Erstellung eines primären Hauptschlüssels und mehrere Unterschlüsseln wird als best-practice betrachtet. Auf diese Weise können die Subkeys auf weiteren Systemen genutzt werden, ohne die Sicherheit des Hauptschlüssels zu kompromitieren. 

<!-- more -->

Außer dem Hauptschlüssel sollten also folgende Schlüssel generiert werden:

- (S) ECDSA Curve 25519 Sign, zum Unterschreiben von Commits und Emails.
- (E) RSA 4096 Encrypt, um Dateien und Emals zu verschlüsseln. 
- (A) ECDSA Curve 25519 Authenticate, zur Authentifizierung gegen Dienste.

Warum RSA 4096 um Dateien und Emails zu verschlüsseln? RSA ist weiter verbreitet also die neueren (und sicherern) Schlüssel die auf Ellyptic Curve Cryptography basieren. Um zu gewährleisten dass Empfänger und Sender auch miteinander kommunizieren können, ist diese Herangehensweise ein aufgrund der immer noch sehr hohen Sicherheit von 4096 bit ein meiner Meinung nach akzeptabler Kompromis.


## Voller Beispielablauf für die Erstellung eines initialen Hauptschlüssels
```bash
gpg --full-gen-key --expert                                                                                                                                 ─╯
gpg (GnuPG) 2.2.27; Copyright (C) 2021 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
   (9) ECC and ECC
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (13) Existing key
  (14) Existing key from card
Your selection? 11

Possible actions for a ECDSA/EdDSA key: Sign Certify Authenticate 
Current allowed actions: Sign Certify 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? s

Possible actions for a ECDSA/EdDSA key: Sign Certify Authenticate 
Current allowed actions: Certify 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: John Doe
Email address: john@doe.com
Comment: Example
You selected this USER-ID:
    "John Doe (Example) <john@doe.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 5C36D9C703EB00BB marked as ultimately trusted
gpg: revocation certificate stored as '/home/flowinho/.gnupg/openpgp-revocs.d/8101FE04B54461BA57FC8AD05C36D9C703EB00BB.rev'
public and secret key created and signed.

pub   ed25519 2023-05-25 [C]
      8101FE04B54461BA57FC8AD05C36D9C703EB00BB
uid                      John Doe (Example) <john@doe.com>

```

## Voller Beispielablauf für die Erstellung der Unterschlüssel

```bash
gpg --expert --edit-key 8101FE04B54461BA57FC8AD05C36D9C703EB00BB                                                                                            ─╯
gpg (GnuPG) 2.2.27; Copyright (C) 2021 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   3  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 3u
sec  ed25519/5C36D9C703EB00BB
     created: 2023-05-25  expires: never       usage: C   
     trust: ultimate      validity: ultimate
[ultimate] (1). John Doe (Example) <john@doe.com>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 11

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: Sign 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/5C36D9C703EB00BB
     created: 2023-05-25  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  ed25519/2C115828E2B7C263
     created: 2023-05-25  expires: never       usage: S   
[ultimate] (1). John Doe (Example) <john@doe.com>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 8

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Sign Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? s

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
Key does not expire at all
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/5C36D9C703EB00BB
     created: 2023-05-25  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  ed25519/2C115828E2B7C263
     created: 2023-05-25  expires: never       usage: S   
ssb  rsa4096/0463C812DEED3460
     created: 2023-05-25  expires: never       usage: E   
[ultimate] (1). John Doe (Example) <john@doe.com>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 11

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: Sign 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? s

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? a

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: Authenticate 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/5C36D9C703EB00BB
     created: 2023-05-25  expires: never       usage: C   
     trust: ultimate      validity: ultimate
ssb  ed25519/2C115828E2B7C263
     created: 2023-05-25  expires: never       usage: S   
ssb  rsa4096/0463C812DEED3460
     created: 2023-05-25  expires: never       usage: E   
ssb  ed25519/CAE66B5B6D24DF8C
     created: 2023-05-25  expires: never       usage: A   
[ultimate] (1). John Doe (Example) <john@doe.com>

gpg> save
```

## Generierung Revocation Certificate

```bash
gpg --output john@doe.com.revocation.asc --gen-revoke 8101FE04B54461BA57FC8AD05C36D9C703EB00BB                                                              ─╯

sec  ed25519/5C36D9C703EB00BB 2023-05-25 John Doe (Example) <john@doe.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 0
Enter an optional description; end it with an empty line:
> 
Reason for revocation: No reason specified
(No description given)
Is this okay? (y/N) y
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!

```

## Backup der Schlüssel

Publickey exportieren

```bash
gpg --output john@doe.com.public.pgp --armor --export john@doe.com
```

Secretkey exportieren

```bash
gpg --output john@doe.com.private.pgp --armor --export-secret-key john@doe.com
```

Backup exportieren

```bash
gpg --output john@doe.com.backupkeys.pgp --armor --export-secret-keys --export-options export-backup john@doe.com
```
