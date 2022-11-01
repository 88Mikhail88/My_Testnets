## Nibiru monitoring/alerting system

### install docker
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
### configuring tenderduty
```bash
cd && mkdir tenderduty && cd tenderduty
docker run --rm ghcr.io/blockpane/tenderduty:latest -example-config >config.yml
# edit config.yml and add chains, notification methods etc.
nano $HOME/tenderduty/config.yml
```
### exemple config.yml (do not copy)
```bash
chains:
  "Nibiru":                                                                      # name
    chain_id: nibiru-testnet-1                                                   # chain id
    valoper_address: nibivaloper1ht02h4cnxdc86pl3pkyc528hnnnm3kxs089x86          # valoper_address
    public_fallback: no
    alerts:
      stalled_enabled: yes
      stalled_minutes: 10
      consecutive_enabled: yes
      consecutive_missed: 5
      consecutive_priority: critical
      percentage_enabled: no
      percentage_missed: 10
      percentage_priority: warning
      alert_if_inactive: yes
      alert_if_no_servers: yes

      pagerduty:
        enabled: yes
        api_key: "" # uses default if blank

      discord:
        enabled: yes
        webhook: "" # uses default if blank

      telegram:
        enabled: yes
        api_key: "" # uses default if blank
        channel: "" # uses default if blank

    # This section covers our RPC providers. No LCD (aka REST) endpoints are used, only TM's RPC endpoints
    # Multiple hosts are encouraged, and will be tried sequentially until a working endpoint is discovered.
    nodes:
      # URL for the endpoint. Must include protocol://hostname:port
      - url:                                                                     # URL for the endpoint
        # Should we send an alert if this host isn't responding?
        alert_if_down: yes
      # repeat hosts for monitoring redundancy
      - url: 
        alert_if_down: yes
```
### run docker and check logs
```
docker run -d --name tenderduty -p "8888:8888" --restart unless-stopped -v $(pwd)/config.yml:/var/lib/tenderduty/config.yml ghcr.io/blockpane/tenderduty:latest
docker logs -f --tail 20 tenderduty
```
### open your browser and enter SERVER_IP_ADDRESS:8888

![](https://github.com/88Mikhail88/My_Images/blob/main/Nibiru/Screenshot_15.png)

![](https://github.com/88Mikhail88/My_Images/blob/main/Nibiru/Screenshot_1.png)

![](https://github.com/88Mikhail88/My_Images/blob/main/Nibiru/Screenshot_2.png)
