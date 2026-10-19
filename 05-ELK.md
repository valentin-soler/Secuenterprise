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

Vous pouvez obtenir un nouveau mot de passe pour Elastic via la commande :
```
/usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
```

### Configuration de Kibana

Dans le fichier `/etc/kibana/kibana.yml` vous devez définir la valeur d'écoute de Kibana
```
server.host: "0.0.0.0"
```

Vous pouvez maintenant démarrer le service de Kibana
```
systemctl enable --now kibana
```

En allant sur `<ip-server>:5601` vous devriez tomber sur cette page qui vous demande un jeton d'enregistrement

![Page jeton enregistrement](img/Capture%20d'écran_20261018_211753.png)

Vous pouvez obtenir ce jeton grace à la commande `/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana`

On va aussi vous demandez un 2nd code, que vous pouvez obtenir grace à la commande `/usr/share/kibana/bin/kibana-verification-code`

![Page demande de code Kibana](img/Capture%20d'écran_20261018_212139.png)

Vous pouvez maintenant patienté le temps qu'Elastic et Kibana finissent de démarré et de se configurer

![Page fin de config](img/Capture%20d'écran_20261018_212322.png)

Une fois fini vous devriez tombé sur cette page de connection, pour vous connecté vous pouvez utilisé vos identifiant Elastic

![Page login Kibana](img/Capture%20d'écran_20261018_212609.png)
![Page acceuil Kibana](img/Capture%20d'écran_20261018_212817.png)

### Configuration de Logstash

Vous devez renseigner la configuration de votre pipeline afin que les fichiers arrivent bien sur votre serveur `/etc/logstash/conf.d/beats.conf`
```
input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["https://localhost:9200"]
    user => "elastic"
    password => "REMPLACE_PAR_LE_MOT_DE_PASSE_ELASTIC"
    ssl_certificate_authorities => ["/etc/elasticsearch/certs/http_ca.crt"]
    index => "filebeat-%{+YYYY.MM.dd}"
  }
}
```

Vous pouvez lancer directement logstach via son service 
```
systemctl enable --now logstash
```
