# Installation de Graphana + Intégration des donnée de Prometheus

## Installation de Graphana

Pour que le dépot fonctionne, il faut que le paquet ```gnupg``` sois présent sur la machine ```apt install gnupg```
```
mkdir -p /etc/apt/keyrings
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor > /etc/apt/keyrings/grafana.gpg
chmod 644 /etc/apt/keyrings/grafana.gpg
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | tee /etc/apt/sources.list.d/grafana.list
apt update
```

Le dépot est maintenan présent, vous pouvez le voir lors de la mise a jours des paquets.

Vous pouvez maintenant installer Graphana 
```
apt install -y grafana
systemctl enable --now grafana-server
```

Vous avez maintenant installé Graphana ! les logins par défauts sont : `admin:admin`

![Page de login Graphana](img/Capture%20d'écran_20261016_141456.png)

## Ajout de Prometheus comme source de données

Allez dans `Connection -> Data Sources -> Add Data Sources`

![Page Data sources sans sources](img/Capture%20d'écran_20261016_142321.png)

Choisissez "Prometheus"
![Choix Prometheus](img/Capture%20d'écran_20261016_142452.png)

Renseigner le lien du serveur Prometheus
![Lien Prom](img/Capture%20d'écran_20261016_142654.png)

Vous pouvez maintenant voirs la source de présente
![](img/Capture%20d'écran_20261016_142821.png)

Vous pouvez aussi crée un Dashboard afin d'obtenir un affichage comme cela
![](img/Capture%20d'écran_20261016_143403.png)