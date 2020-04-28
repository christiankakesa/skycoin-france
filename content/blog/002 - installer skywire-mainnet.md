+++
title = "Installer Skywire Mainnet"
date = "2020-04-28T08:00:00+02:00"
tags = ["skywire", "visor", "hypervisor"]
categories = ["skywire"]
description = "Quelques détails sur l'installation de Skywire mainnet depuis les binaires ou le code source."
banner = "img/content/002/002-skywire-mainnet.jpeg"
+++

Cette installation se base sur _Raspbian_, une distribution GNU/Linux basée sur Debian.

Dans notre cas elle sera installée sur une _Raspberry Pi Zero W_.

Si vous utilisez une autre distribution _GNU/Linux_, adaptez l'installation des _packages_ selon votre _OS_.

![Raspberry Pi Zero W](/img/content/002/002-raspberry_pi_zero_w.jpg)

## Installer Skywire Mainnet

> Si vous avez une _Raspberry Pi Zero_, passez directement à la section installation depuis le code source.

### Installer Skywire Mainnet depuis les binaires

> Je vais utiliser les binaires _ARMv7_ qui ne fonctionnent pas sur une _Pi Zero_ qui embarque un _ARMv6_
> mais les commandes à exécuter sont exactement les mêmes.

Ouvrez un terminal et installez les dépendances avec les commandes ci-dessous :

```bash
sudo apt update
sudo apt install wget
```

#### Récupérer les binaires depuis le Github du projet

Nous allons récupérer les binaires depuis le Github du projet ([Releases Skywire-Mainnet](https://github.com/SkycoinProject/skywire-mainnet/releases)) :

Créons les répertoires nécessaires à notre installation :

```bash
cd $HOME
mkdir bin
mkdir -p opt/skywire
```

Téléchargez et décompressez les binaires :

```bash
wget https://github.com/SkycoinProject/skywire-mainnet/releases/download/v0.2.3/skywire-v0.2.3-linux-arm.tar.gz
tar -zxvf skywire-v0.2.3-linux-arm.tar.gz -C /$HOME/opt/skywire
```

Créez des liens symboliques des binaires vers le répertoire _$HOME/bin_ :

```bash
ln -s /home/pi/opt/skywire/hypervisor /home/pi/bin/hypervisor
ln -s /home/pi/opt/skywire/skywire-cli /home/pi/bin/skywire-cli
ln -s /home/pi/opt/skywire/skywire-visor /home/pi/bin/skywire-visor
```

Éditez le fichier _$HOME/.bashrc_ et ajoutez la ligne ci-dessous à la fin ; `nano $HOME/.bashrc` ; sauvegardez avec `CTRL+o` (pas zéro, _o_ pour **o**verwrite), puis sortez avec `CTRL+x` :

```bash
# $HOME/bin
export PATH=$HOME/bin:$PATH
```

Depuis le même terminale _sourcez_ le fichier _$HOME/.bashrc_ et tester un des programmes de Skywire :

```bash
source $HOME/.bashrc
hypervisor help
```

La page d'aide du programme doit s'afficher.

Vous pouvez passer à la section [Configurer l'Hypervisor et le Visor](#configurer-lhypervisor-et-le-visor)

### Installer Skywire Mainnet depuis le code source

Ouvrez un terminal et installez les dépendances avec les commandes ci-dessous :

```bash
sudo apt update
sudo apt install git wget make
```

#### Installer _Go_ sur Linux

> Go est un langage de programmation puissant et très simple à maîtriser.
> Son installation respecte les mêmes principes 👌 !

```bash
cd $HOME
wget https://dl.google.com/go/go1.14.2.linux-armv6l.tar.gz
sha256sum go1.14.2.linux-armv6l.tar.gz
echo "eb4550ba741506c2a4057ea4d3a5ad7ed5a887de67c7232f1e4795464361c83c"
```

Vérifiez que la sortie de la commande `sha256sum go1.14.2.linux-armv6l.tar.gz` affiche la même valeur que la colonne _SHA256 Checksum_ de votre version de Go.

Si les _checksum_ ne _matchent_ pas !? Arrêtez-vous là, le fichier doit être corrompu, contactez Google pour les notifier du problème !

Décompressez l'archive :

```bash
tar -zxvf go1.14.2.linux-armv6l.tar.gz
```

Éditez le fichier _$HOME/.bashrc_ et ajoutez ces lignes à la fin `nano $HOME/.bashrc` ; sauvegardez avec `CTRL+o` (pas zéro, _o_ pour **o**verwrite), puis sortez avec `CTRL+x` :

```bash
# Go
export GOPATH=$HOME/go
export PATH=${GOPATH}/bin:$PATH
```

Depuis le même teminale _sourcez_ le fichier _$HOME/.bashrc_ une fois que vous l'avez enregistré :

```bash
source $HOME/.bashrc
go version
```

Vous devez obtenir la version de _Go_ que vous venez d'installer : `go version go1.14.2 linux/arm`.

#### Récupérer et conpiler Skywire Mainnet

Nous allons maintenant compiler _Skywire Mainnet_ :

```bash
cd $HOME
mkdir -p opt/skywire
mkdir src
cd src
git clone https://github.com/SkycoinProject/skywire-mainnet.git
cd skywire-mainnet
make release
make install
```

Vérifiez que l'installation s'est bien passée avec la commande : `skywire-cli --help`.

Une page d'aide devrait s'afficher sur le terminal.

## Configurer l'Hypervisor et le Visor

Nous allons maintenant passer à la configuration d'un _hypervisor_ et d'un _visor_.

Créons le répertoire où nous allons déposer nos fichiers de configuration :

```bash
cd $HOME
cd opt/skywire
hypervisor gen-config
skywire-cli visor gen-config
```

La commande `hypervisor gen-config` a génèré le fichier de configuration de l'hypervisor (le contenu de mon fichier _hypervisor-config.json_ est différent du votre) :

```json
{
 "public_key": "02535fbd9ea996bebb0caf5b8036740aa42b283c4535e381346710dd12f0a71a44",
 "secret_key": "x",
 "db_path": "/home/pi/opt/skywire/users.db",
 "enable_auth": false,
 "cookies": {
  "hash_key": "a4db26cec4ef43770aaa0d548c12da0b5264d1f9c1185f273c9ef50e6c1e55abac3c8d430907f8608135fbcae70cbf12e83c5cea402d829b246c5e26a3764b39",
  "block_key": "04e0f4da6f8d3bf4dd91c89e6118c2da4f11c1dad65f8d3ce09748c0323c8882",
  "expires_duration": 43200000000000,
  "path": "/",
  "domain": ""
 },
 "dmsg_discovery": "http://dmsg.discovery.skywire.skycoin.com",
 "dmsg_port": 46,
 "http_addr": ":8000",
 "enable_tls": false,
 "tls_cert_file": "",
 "tls_key_file": ""
}
```

Copiez la clé publique, la valeur à droite de _public\_key_ : **02535fbd9ea996bebb0caf5b8036740aa42b283c4535e381346710dd12f0a71a44**, que nous allons renseigner dans la configuration du visor. Cela permettra à l'hypervisor d'avoir les permissions nécessaires pour le contrôler.

La commande `skywire-cli visor gen-config` a généré le fichier de configuration du visor : _skywire-config.json_.

Éditez et recherchez la section `"hypervisors": [],` en bas du fichier et remplacez comme en dessous avec la clé publique que vous avez copié dans le fichier de configuration de l'hypervisor ; sauvegardez avec `CTRL+o` (pas zéro, _o_ pour **o**verwrite), puis sortez avec `CTRL+x` :

```json
"hypervisors": [
    {
        "public_key": "02535fbd9ea996bebb0caf5b8036740aa42b283c4535e381346710dd12f0a71a44",
        "address": ""
    }
],
```

Testez le démarrage de l'ypervisor avec la commande suivante : `hypervisor --config hypervisor-config.json`.

Vous ne devriez pas avoir de message d'erreur.

Lancez votre navigateur internet préféré, Chromium, Chrome, Firefox, Opera ou autres, et saisissez l'IP de votre machine où vous lancez l'hypervisor et le port `8000`, dans mon cas : _<http://192.168.1.101:8000>_.

Vous devez avoir l'interface de votre hypervisor (sans visor pour le moment).

![Hypervisor gui](/img/content/002/002-hypervisor-gui.png)

Pour simplifier ce _tuto_, nous n'allons pas utliser de terminaux virtuel pour exécuter en même temps le visor.

Faites un `CTRL+C` pour arrêter l'hypervisor.

Démarrez le noeud visor avec la commande suivante : `skywire-visor skywire-config.json`.
Vous ne devez pas avoir d'erreurs, ignorez les _warning_ en jaune.

Faites un `CTRL+C` pour arrêter le visor, nous allons passer à la configuration du démarrage automatique.

## Configurer le démarrage automatique des services

Nous utiliserons _systemd_ pour gérer le démarrage automatique de l'hypervisor et du visor.

### Systemd pour l'Hypervisor

Éditez le fichier de service _systemd_ :

```bash
sudo nano /etc/systemd/system/skywire-hypervisor.service
```

Collez-y le contenu ci-dessous (adaptez les entrées, `User`, `Group`, `WorkingDirectory` et `ExecStart`), sauvegardez avec `CTRL+o` (pas zéro, _o_ pour **o**verwrite), puis sortez avec `CTRL+x`:

```ini
[Unit]
Description=Skywire Hypervisor
Wants=network-online.target
After=network.target network-online.target

[Service]
User=pi
Group=pi
WorkingDirectory=/home/pi/opt/skywire
ExecStart=/home/pi/go/bin/hypervisor --config /home/pi/opt/skywire/hypervisor-config.json
KillMode=process
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=skywire-hypervisor
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Activez et lancez le service de l'hypervisor :

```bash
sudo systemctl enable skywire-hypervisor.service
sudo systemctl daemon-reload
sudo systemctl start skywire-hypervisor.service
```

Vérifiez que le statut est _actif_ avec la commande suivante : `sudo systemctl status skywire-hypervisor.service` (appuyez sur la touche `q` ou `CTRL+c` pour sortir).

### Systemd pour le Visor

Éditez le fichier de service _systemd_ :

```bash
sudo nano /etc/systemd/system/skywire-visor.service
```

Collez-y le contenu ci-dessous (adaptez les entrées, `User`, `Group`, `WorkingDirectory` et `ExecStart`), sauvegardez avec `CTRL+o` (pas zéro, _o_ pour **o**verwrite), puis sortir avec `CTRL+x`:

```ini
[Unit]
Description=Skywire Visor
Wants=network-online.target
After=network.target network-online.target

[Service]
User=pi
Group=pi
WorkingDirectory=/home/pi/opt/skywire
ExecStart=/home/pi/go/bin/skywire-visor /home/pi/opt/skywire/skywire-config.json
KillMode=process
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=skywire-visor
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Activez et lancez le service du visor :

```bash
sudo systemctl enable skywire-visor.service
sudo systemctl daemon-reload
sudo systemctl start skywire-visor.service
```

Vérifiez que le statut est _actif_ avec la commande suivante : `sudo systemctl status skywire-visor.service` (appuyez sur la touche `q` ou `CTRL+c` pour sortir).

Si tout s'est bien passé, vous devez avoir le visor listé à l'adresse de l'hypervisor depuis votre navigateur. Dans mon cas : _<http://192.168.1.101:8000>_.

#### Bienvenue sur le réseau Skywire

![Visor listé dans l'Hypervisor](/img/content/002/002-hypervisor_et_son_visor.png)
