---
layout: post
title: Root Galaxy A14
description: How to root a Galaxy A14 and use frida on it.
summary: How to root a Galaxy A14 and use frida on it.
tags: android
---

# attention
Le téléphone va être réinitialisé deux fois.
Si le texte ne suffit pas:
- https://www.youtube.com/watch?v=R8-UNyKA6aY : pour rooter le téléphone
- https://www.youtube.com/watch?v=CHTfYBB-8n0 : pour désactiver l'OEM

# pré-requis
## télécharger le firmware
Télécharger le firmware similaire à celui du téléphone.
Pour obtenir la verion exacte, aller sur le téléphone dans `paramètres->à propos du téléphone->informations sur le logiciel->version de la bande base`:`A145RXXU1AWD1`
https://samfw.com/firmware/SM-A145R/SFR/A145RXXU1AWD1
```sh
SAMFW.COM_SM-A145R_SFR_A145RXXU1AWD1_fac.zip
unzip SAMFW.COM_SM-A145R_SFR_A145RXXU1AWD1_fac.zip

ll
-rw-rw-r--. 1 pouetpouet pouetpouet 6038610042 18 avril 21:00 AP_A145RXXU1AWD1_A145RXXU1AWD1_MQB64354567_REV00_user_low_ship_MULTI_CERT_meta_OS13.tar.md5
-rw-rw-r--. 1 pouetpouet pouetpouet    4014192 18 avril 21:00 BL_A145RXXU1AWD1_A145RXXU1AWD1_MQB64354567_REV00_user_low_ship_MULTI_CERT.tar.md5
-rw-rw-r--. 1 pouetpouet pouetpouet   32327789 18 avril 21:06 CP_A145RXXU1AWD1_CP24089644_MQB64354567_REV00_user_low_ship_MULTI_CERT.tar.md5
-rw-rw-r--. 1 pouetpouet pouetpouet  507545703 18 avril 21:06 CSC_OXM_A145ROXM1AWD1_MQB64354567_REV00_user_low_ship_MULTI_CERT.tar.md5
-rw-rw-r--. 1 pouetpouet pouetpouet        719  7 févr.  2022 _FirmwareInfo_Samfw.com.txt
-rw-rw-r--. 1 pouetpouet pouetpouet  507504748 18 avril 21:06 HOME_CSC_OXM_A145ROXM1AWD1_MQB64354567_REV00_user_low_ship_MULTI_CERT.tar.md5
```

## téléchager les outils nécessaires
```sh
sudo apt install adb frida android-sdk-build-tools-common
```

`odin` pour linux: https://forum.xda-developers.com/t/official-samsung-odin-v4-1-2-1-dc05e3ea-for-linux.4453423/
```sh
wget https://forum.xda-developers.com/attachments/odin-zip.5629297/
unzip odin-zip
```

`magisk`: https://github.com/topjohnwu/Magisk/
```sh
wget https://github.com/topjohnwu/Magisk/releases/download/v26.2/Magisk-v26.2.apk
```

# rooting
## étape 1: préparer le fichier AP rooté
- brancher le téléphone au PC via USB
- copier l'apk `magisk` et le fichier `AP_A145RXXU1AWD1_[ . . . ]_OS13.tar.md5` sur le téléphone dans `Téléchargements`
- installer l'apk `magisk` en cliquant sur le fichier à partir du navigateur de fichiers sur le téléphone (il faut autoriser l'installation d'applications non vérifiées)
- lancer l'application `magisk`
- cliquer sur le bouton `installer`
- sélectionner le fichier AP
- quitter l'application quand le message `All done!` apparaît
- copier depuis le PC le fichier généré (nom: `magisk_patched-[ . . . ].tar`) par `magisk` sur le téléphone vers le PC

## étape 2: débloquer l'OEM
- activer le mode développeur sur le téléphone:
	- cliquer sept fois sur l'onglet `paramètres->à propos du téléphone->informations sur le logiciel->numéro de version` (un message apparaît quand le mode s'active)
- activer l'option `OEM unlocking` dans `paramètres->options de développement`
- activer l'option `USB debugging` dans `paramètres->options de développement`
- éteindre le téléphone
- brancher le téléphone au PC en USB
- appuyer et laisser enfoncé en même temps les boutons `volume haut et bas` jusqu'à l'apparition d'un écran d'avertissement
- appuyer **plusieurs secondes** sur le bouton `volume haut` pour activer le mode débloqué (OEM)
- appuyer sur le bouton `volume haut` pour confirmer
- activer l'option `USB debugging` dans `paramètres->options de développement` une fois le téléphone réinitialisé

## étape 3: installation du nouveau firmware
- éteindre le téléphone
- brancher le téléphone au PC en USB
- appuyer et laisser enfoncé en même temps les boutons `volume haut et bas` jusqu'à l'apparition d'un écran d'avertissement
- appuyer sur le bouton `volume haut` pour continuer
- vérifier que le mode `OEM LOCK` est sur `OFF (U)`
- lancer `odin` sur le PC:
```sh
./odin4 -a magisk_patched-[ . . . ].tar -b BL_A145RXXU1AWD1_A145RXXU1AWD1_MQB64354567_REV00_user_low_ship_MULTI_CERT.tar.md5 -c CP_A145RXXU1AWD1_CP24089644_MQB64354567_REV00_user_low_ship_MULTI_CERT.tar.md5 -s CSC_OXM_A145ROXM1AWD1_MQB64354567_REV00_user_low_ship_MULTI_CERT.tar.md5
```
(si tout se passe bien le PC devrait afficher un pourcentage de progression, et le téléphone une barre de progression)
- dérouler la procédure d'installation du téléphone
- copier l'apk `magisk` sur le téléphone dans `Téléchargements`
- installer l'apk `magisk` en cliquant sur le fichier à partir du navigateur de fichiers sur le téléphone (il faut autoriser l'installation d'applications non vérifiées)
- lancer l'application `magisk`
- vérifier que le bouton `désinstaller magisk` est présent, signe que le téléphone est rooté

## étape 4: préparer le téléphone pour l'utilisation de frida
- sur le téléphone: activer le mode développeur :
	- cliquer sept fois sur l'onglet `paramètres->à propos du téléphone->informations sur le logiciel->numéro de version` (un message apparaît quand le mode s'active)
- sur le téléphone: activer l'option `USB debugging` dans `paramètres->options de développement`
- sur le PC: vérifier que la commande `adb devices` renvoie bien `[un id quelconque]    device` et non pas `[un id quelconque]    unauthorized`
- sur le PC: 
	- déterminer l'architecture du téléphone:
	```sh
	adb shell getprop ro.product.cpu.abi
	arm64-v8a
	```
	- télécharger le fichier `frida-server` correspondant: https://github.com/frida/frida/releases (pour nous: https://github.com/frida/frida/releases/download/16.1.3/frida-server-16.1.3-android-arm64.xz)
	```sh
	wget https://github.com/frida/frida/releases/download/16.1.3/frida-server-16.1.3-android-arm64.xz
	xz -d frida-server-16.1.3-android-arm64.xz
	mv frida-server-16.1.3-android-arm64 frida-server
	```
	- pousser le fichier `frida-server` sur le téléphone:
	```sh
	adb push frida-server /data/local/tmp
	```
	- lancer un shell avec `adb`:
	```sh
	adb shell
	```
- sur le téléphone: autoriser le service `shell` à être `superuser`: activer `com.android.shell` dans l'application `magisk`: onglet `Super-Utilisateur`
- sur le PC: exécuter le binaire `frida-server`:
	```sh
	adb shell
	su
	cd /data/local/tmp
	chmod 755 frida-server
	./frida-server
	exit
	exit
	frida-ps -U

	[renvoie la liste des services sur le téléphone]
	```