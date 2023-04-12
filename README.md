# Yubikey-2FA
Procédure pour paramétrer sa Yubikey pour le déchiffrement de son disque ainsi que les logins (sudo, session, ...) sous Linux

# Déchiffrement du disque avec la Yubikey
## Préparation

Avant de commencer le paramétrage il est nécessaire d'installer le paquet **yubikey-full-disk-encryption** sous Arch Linux.  
  
```bash
sudo pacman -Syu yubikey-full-disk-encryption
``` 
  
Pour les autres distros, le nom du paquet peut changer, il faudra se renseigner sur le site de Yubico pour le récupérer.
> NB: il est possible de récupérer le paquet via GitHub.

## Configuration en mode Challenge-Response d'un slot de la Yubikey 

Il va falloir configurer un slot de la Yubikey qui contiendra une clé secrète d'une taille de 20 bytes. Cette clé sera utilisée lorsque vous créerez votre mot de passe.  
**Attention à sélectionner le slot qui vous convient par défaut la commande configure le slot 2**  
  
Voici la commande :  
  
```bash
ykpersonalize -v -2 -ochal-resp -ochal-hmac -ohmac-lt64 -oserial-api-visible -ochal-btn-trig
```
  
* Verbose output (`-v`)
* Use Slot 2 (`-2`)
* Set Challenge-Response mode (`-ochal-resp`)
* Generate HMAC-SHA1 challenge responses (`-ochal-hmac`)
* Calculate HMAC on less than 64 bytes input (`-ohmac-lt64`)
* Allow YubiKey serial number to be read using an API call (`-oserial-api-visible`)
* Require touching YubiKey before issue response (`-ochal-btn-trig`) (optional)

## Configuration du fichier /etc/ykfde.conf

Le fichier fourni est prêt à l'emploi, néanmoins vous pouvez modifier certaines valeurs pour matcher avec vos préférences.  

Si vous avez configuré un autre slot que le n°2, il faudra spécifier le slot dans le fichier de configuration. Pour cela il faut modifier la ligne `YKFDE_CHALLENGE_SLOT=`  
  
Les deux paramètres sur lequelles vous pouvez agir sont :  
* `YKFDE_CRYPTSETUP_TRIALS=` Il configure le nombre de tentative possible.
*  `YKFDE_CHALLENGE_YUBIKEY_INSERT_TIMEOUT=` Il configure le temps d'attente pour détecter la Yubikey. je recommande de laisser la valeur à 5s cela permet, dans le cas où vous n'avez pas la clé de pouvoir boot plus rapidement.  

Une fois que les paramètres vous correspondent, il faut regénérer l'initramfs avec la commande :  
```bash
sudo mkinitcpio -P
```

## Configuration de la Yubikey sur un LUKS existant

Pour configurer un mot de passe avec la Yubikey sur un LUKS déjà configuré, il faut utiliser la commande `ykfde-enroll` :  
  
```
ykfde-enroll -d /dev/<disque> -s <slot_yubikey>
```

Pour tester le bon fonctionnement, faites la commande : 
```
ykfde-open -d /dev/<disque>
```
  
## Ajout du hook ykfde

Maintenant que la configuration est terminée, il faut ajouter le hook `ykfde` dans votre fichier `/etc/mkinitcpio.conf`. Vous devez le placer avant le hook `encrypt`.  
  
Une fois votre fichier configuré, il faur regénérer votre initramfs avec la commande :  
  
```
sudo mkinitcpio -P
```
  
## Redémarrage

Toutes les étapes pour configurer le déchiffrement de votre disque avec la yubikey sont terminées. Vous pouvez maintenant reboot votre PC et tester votre installation.

# Configuration du PAM avec la Yubikey
