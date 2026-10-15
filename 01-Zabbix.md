# Installation de Zabbix (Server + Agent)

## Installation de Zabbix server

Nous allons installer Zabbix server sur une machine sous Debian 13 :
J'ai fait le choix de prendre la 7.0 LTS et non 7.4 pour des raisons de maintenance, une version LTS étant supporté plus longtemps et plus "stable"

### Mise à jour du système et installation des prérequies

```apt update & apt full-upgrade -y```

```apt install -y wget ca-certificates```

### Installation des dépots de Zabbix (dans mon cas 7.0 LTS)

```wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian13_all.deb```

```dpkg -i zabbix-release_latest_7.0+debian13_all.deb```

```apt update```

### Installation de MariaDB && Zabbix

Zabbix à besoin de MariaDB pour fonctionner, cela permet de stocker les données de la DB de Zabbix.

```apt install -y mariadb-server apache2 zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent2```

```systemctl enable --now mariadb```

```mariadb-secure-installation``

### Installation de la base de données de Zabbix.

Zabbix donne un template de la base de donnée à intégré dans MariaDB afin que Zabbix puisse fonctionner.

```mariadb -u root -p ```

```CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;```

Pensez à remplacer le mot de passe par le votre.

```CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'votre_mot-de-passe';```

```GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';```

```QUIT;```

Nous allons maintenant importer la base de donnée dans MariaDB, ce template nous évite de crée les différente tables et colonnes à la mains.

```zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mariadb --default-character-set=utf8mb4 -uzabbix -p zabbix```

Cela peut prendre un peu de temps, vous devez bien attendre que l'invite de commande sois de retours et qu'il n'y ai aucune erreur pour que l'importation sois bien faites.

![Capture import DB](img/Capture%20d'écran_20261015_114406.png)

Vous devez renseignez le MDP de votre DB dans /etc/zabbix/zabbix_server.conf (DBPassword)
### Démarrage de Zabbix et son interface web

```systemctl enable --now zabbix-server zabbix-agent2 apache2 ```

```a2enconf zabbix & systemctl reload apache2```

### Configuration de Zabbix

Maintenant que notre serveur Zabbix est installé, nous devons le configuré, pour cela nous pouvons le faire via son interface web disponible sur http://<votre-ip>/zabbix
![1er page du setup](img/Capture%20d'écran_20261015_115726.png)

Vérifié que tout les prérequis sois OK
![Page des pré-requis](img/Capture%20d'écran_20261015_115946.png)

Vous pouvez maintenant renseigner les info de votre base de donnée afin que Zabbix puisse s'y connecter.
![Page de config. DB](img/Capture%20d'écran_20261015_121252.png)

Vous pouvez maintenant donner un nom et choisir le thème de l'interface.
![Page confg. nom + theme](img/Capture%20d'écran_20261015_121351.png)

Et voila votre serveur est installé !
![Page final config.](img/Capture%20d'écran_20261015_121454.png)

### Premiére connection + Changement des identifiant par défaut

Vous allez maintenant tomber sur cette page:
![Page de login](img/Capture%20d'écran_20261015_121742.png)

Vous devez utiliser le combot Admin/zabbix pour vous connecter (attention le A majuscule doit bien être mis !)
Vous allez alors tomber sur la page principal de Zabbix 
![Panel admin](img/Capture%20d'écran_20261015_122326.png)

Nous allons maintenant changer le mot de passe par défaut en passant pas "Paramètres utilisateur -> Profil -> Changer le mot de passe"
![Changement de MDP](img/Capture%20d'écran_20261015_122532.png)

Nous pouvons maintenant passer à l'installation de l'Agent sur nos machine client

## Installation & Configuration de l'Agent Zabbix sous Debian 13

### Installation de l'Agent

Pour l'installation de l'Agent sur Debian 13, tout comme le serveur nous avons besoin d'installer le dépots, mais cette fois nous allons installer seulement l'agents.

```wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian13_all.deb```

```dpkg -i zabbix-release_latest_7.0+debian13_all.deb```

```apt update```

```apt install -y zabbix-agent2```

### Configuration de l'agent

Nous allons maintenant configurer l'agent afin qu'il communique avec notre serveur Zabbix.
Le choix du nom de la machine doit correspondre avec le nom que vous allez lui donner sur votre serveur Zabbix, sinon cela ne fonctionnera pas

Dans `/etc/zabbix/zabbix_agent2.conf`, vous devez modifier les 3 champs suivants:

- Server (`Server=<ip de votre serveur>`)
- Server Active (`ServerActive=<ip de votre serveur>`)
- Hostname (`Hostname=nom-de-votre-machine`)

Nous pouvons maintenant démarré l'agent:

`systemctl enable --now zabbix-agent2`

### Création de l'hote sur notre serveur

Pour cela, vous devez allez dans `Collecte de données --> Hôtes --> Crée un hôte`

![Page Hôtes](img/Capture%20d'écran_20261015_133109.png)

Vous allez maintenant renseigner le nom de votre hôte (que vous avez défini précédament), son groupe, le moyen de connection via l'agent et son modéle de recupération de log

![Page add Host 1](img/Capture%20d'écran_20261015_133618.png)

Il va maintenant apparaitre dans la liste de vos hôtes. Vous devez faire cette manipulation pour chaqu'un de vos hôtes.

![Page Host avec 1 Hote](img/Capture%20d'écran_20261015_133717.png)

Une fois tout vos hôtes mis, vous devriez les voirs apparaitre dans hôte
![Page avec tout les hotes](img/Capture%20d'écran_20261015_133717.png)
![Page d'acceuil](img/Capture%20d'écran_20261015_134840.png)