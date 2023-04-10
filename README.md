# Yubikey-2FA
Procédure pour paramétrer sa Yubikey pour le déchiffrement de son disque ainsi que les logins (sudo, session, ...) sous Linux

# Mise en place du déchiffrement
## Préparation

Avant de commencer le paramétrage il est nécessaire d'installer le paquet **yubikey-full-disk-encryption** sous Arch Linux.  
  
`sudo pacman -Syu yubikey-full-disk-encryption`  
  
Pour les autres distros, le nom du paquet peut changer, il faudra se renseigner sur le site de Yubico pour le récupérer.
> NB: il est possible de récupérer le paquet via GitHub.

## Configuration en mode Challenge-Response d'un slot de la Yubikey 

Il va falloir configurer un slot de la Yubikey qui contiendra une clé secrète d'une taille de 20 bytes. Cette clé sera utilisée lorsque vous créerez votre mot de passe.  
**Attention à sélectionner le slot qui vous convient par défaut la commande configure le slot 2**  
  
Voici la commande :  
  
`ykpersonalize -v -2 -ochal-resp -ochal-hmac -ohmac-lt64 -oserial-api-visible -ochal-btn-trig`
  
* Verbose output (`-v`)
* Use Slot 2 (`-2`)
* Set Challenge-Response mode (`-ochal-resp`)
* Generate HMAC-SHA1 challenge responses (`-ochal-hmac`)
* Calculate HMAC on less than 64 bytes input (`-ohmac-lt64`)
* Allow YubiKey serial number to be read using an API call (`-oserial-api-visible`)
* Require touching YubiKey before issue response (`-ochal-btn-trig`) (optional)

