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

