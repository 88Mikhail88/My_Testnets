## Haqq monitoring/alerting system

### install docker
```
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
### configuring tenderduty
```
cd && mkdir tenderduty && cd tenderduty
docker run --rm ghcr.io/blockpane/tenderduty:latest -example-config >config.yml
# edit config.yml and add chains, notification methods etc.
nano $HOME/tenderduty/config.yml
```
### run docker and check logs
```
docker run -d --name tenderduty -p "8888:8888" --restart unless-stopped -v $(pwd)/config.yml:/var/lib/tenderduty/config.yml ghcr.io/blockpane/tenderduty:latest
docker logs -f --tail 20 tenderduty
```
### open your browser and enter SERVER_IP_ADDRESS:8888

![](https://github.com/88Mikhail88/My_Images/blob/main/Haqq/Screenshot_3.png)

![](https://github.com/88Mikhail88/My_Images/blob/main/Haqq/Screenshot_2.png)

![](https://github.com/88Mikhail88/My_Images/blob/main/Haqq/Screenshot_1.png)


