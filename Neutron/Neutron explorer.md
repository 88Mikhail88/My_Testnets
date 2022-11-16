# Fork Ping.Pub explorer and adding Neutron network

## Simple way

### Updating packages and installing the environment
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl
```
### Install yarn and nginx
```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add - && \
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list && \
sudo apt update && \
sudo apt install yarn -y

sudo apt install nginx
```
### Clone repository and delete unnecessary stuff
```bash
cd ~
git clone https://github.com/ping-pub/explorer.git
cd explorer

rm -rf src/chains/mainnet/*
rm -rf src/chains/testnet/*
rm -rf public/logos/*
```
### Uploading Neutron [logo](https://github.com/88Mikhail88/My_Testnets/blob/main/Neutron/logo_neutron.png)
```bash
/root/explorer/public/logos/
```
### Uploading Neutron [config](https://github.com/88Mikhail88/My_Testnets/blob/main/Neutron/neutron.json)
```bash
/root/explorer/src/chains/mainnet/
```
### Compiling binary file
```bash
yarn && yarn build
cp -r ./dist/* /var/www/html
systemctl restart nginx.service
```
### Open your browser and enter SERVER_IP_ADDRESS #Example http://5.161.121.34
![](https://github.com/88Mikhail88/My_Images/blob/main/Neutron/Screenshot_1.png)
### If you get a 404 error when the page reloads, run
```bash
cd ~
nano /etc/nginx/sites-available/default
```
![](https://github.com/88Mikhail88/My_Images/blob/main/Neutron/Screenshot_2.png)
```bash
systemctl restart nginx.service
```
