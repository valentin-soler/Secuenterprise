# Installation TheHive + MISP

Pour TheHive, la machine virtuel est passé à 4vCore + 16 Go de ram a cause de la lourdeur des deux logiciel

![Spec. VM](img/Capture%20d'écran_20261019_150756.png)

Nous allons utiliser docker pour facilité notre déployement, TheHive & MISP étant disponible en utilisation sur Docker.

## Installation de Docker

Nous allons installer Docker depuis les depôt officiel afin d'utiliser la derniére version et non la version présente dans les depot de Debian qui ne sont pas forcément à jours.
```
sudo apt install -y ca-certificates curl git jq openssl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg \
  -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
sudo tee /etc/apt/sources.list.d/docker.sources >/dev/null <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: trixie
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable --now docker
sudo usermod -aG docker "$USER"
```

## Installation de TheHive
```
mkdir -p ~/soc
cd ~/soc
git clone https://github.com/StrangeBeeCorp/docker.git
cd docker/prod1-thehive
bash ./scripts/init.sh
```
Vous pouvez maintenant executer le script d'initialisation, il va crée les bon dossier et les bonne permission pour le conteneur
```
bash ./scripts/init.sh
```
On peut maintenant lancer nos conteneur
```
docker compose pull
docker compose up -d
```

Et voila !!!!!!!!!!!
![](img/Capture%20d'écran_20261019_160157.png)

Vous pouvez utiliser les login par défaut : `admin@thehive.local:secret`

![](img/Capture%20d'écran_20261019_160321.png)

## Installation de MISP
Nous allons maintenant installer MISP, nous allons aussi modifier les ports par défaut pour ne pas interféré avec TheHive, le port 80 -> 8080 et 443 --> 8443
