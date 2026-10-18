# Installation de la stack ELK

## Installation de la stack serveur

### Installation des dépôts et paquet prérequis

Vous devez avoir sur la machine les paquet suivant déjà installé : `curl gnupg ca-certificates`
Si ce n'est pas le cas, vous pouvez les installer via : `apt install curl gnupg ca-certificates`

Nous allons maintenant installer le dépôt:
```
install -d -m 0755 /etc/apt/keyrings
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | gpg --dearmor -o /etc/apt/keyrings/elasticsearch.gpg
chmod 644 /etc/apt/keyrings/elasticsearch.gpg
echo "deb [signed-by=/etc/apt/keyrings/elasticsearch.gpg] https://artifacts.elastic.co/packages/9.x/apt stable main" > /etc/apt/sources.list.d/elastic-9.x.list
apt update
```
Vous devriez maintenant voir le dépôt d'Elastic apparaitre

![Img depot elk](img/Capture%20d'écran_20261018_205936.png)

Vous pouvez maintenant installer ELK (Elasticsearch, Logstash & Kibana)

```
apt install -y elasticsearch kibana logstash
```

Ce sont des paquets assez lourd, qui peuvent prendre du temps a se télécharger.

![](img/Capture%20d'écran_20261018_210231.png)

Et voila ELK est présent sur la machine! Il faut maintenant le configurer !

### Configuration d'Elasticsearch
Nous allons faire une configuration minimal qui est à adapté a votre environement.
Cette configuration doit être mise dans `/etc/elasticsearch/elasticsearch.yml` pour être prise en compte.

```
cluster.name: elk-secuentreprise
node.name: elk01
network.host: 127.0.0.1
discovery.type: single-node
```

Vous pouvez maintenant démarrer ElasticSearch
```
systemctl enable --now elasticsearch
```