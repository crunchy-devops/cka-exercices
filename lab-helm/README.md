# Installer Helm
##Installation de binaire helm
```shell script
sudo apt -y install build-essential
sudo snap install go --classic
wget https://github.com/helm/helm/archive/refs/tags/v3.11.3.tar.gz
tar -zxvf v3.11.3.tar.gz
cd helm-3.11.3/
make
cd bin
sudo cp  helm /usr/local/bin/helm
helm version
```




