# Installation de Prometheus (Server + Agent)

## Installation de Prometheus Server

L'installation ce fait simplement en installant le packet 

```apt install -y prometheus```

Et l'activation du service

```systemctl enable --now prometheus```

Vous aurez maintenant accées a Prometheus via son interface web sur ```http://<ip>:9090```

![Acceuil Prometheus](img/Capture%20d'écran_20261016_120303.png)

## Installation de Node Explorer sur les clients.

Nous allons maintenant installer Node Explorer afin que les clients puissent remonté les infos au serveur Prometheus.

```apt install -y prometheus-node-exporter```

```systemctl enable --now prometheus-node-exporter```

Vous avez normalement les métrics qui sont accésible via ```http://<ip-client>:9100/metrics```

![Exemple de la page des API Metrics](img/Capture%20d'écran_20261016_132839.png)

## Configuration de Prometheus pour récupéré les LOG sur les clients

Vous devez mettre vos client dans le fichier ```/etc/prometheus/prometheus.yml```
Vous devez mettre vos Noeud dans ```scrape_configs:``` comme dans l'exemple si dessous (ne pas toucher celui avec prometheus présent par défaut)

![Exemple de config des clients](img/Capture%20d'écran_20261016_133506.png)

Vous pouvez redémarré le service aprés ```systemctl restart prometheus```

En allant sur ```http://<ip-serveur>:9090/target``` vous devriez voir vos différents clients.
![Page Target](img/Capture%20d'écran_20261016_135030.png)