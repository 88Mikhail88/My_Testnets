## Install Protocol node KYVE Network (Interpool-security)

### Update packages, install dependencies
```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install unzip curl wget -y
```
### Install npm | nodejs
```bash
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash - && \
sudo apt-get install nodejs -y && \
echo -e "\nnodejs > $(node --version).\nnpm  >>> v$(npm --version).\n"
```

### Install Kysor
```bash
cd $HOME
wget wget https://github.com/KYVENetwork/kyvejs/releases/download/%40kyve%2Fkysor%401.0.0-beta.1/kysor-linux-x64.zip
unzip kysor-linux-x64.zip
mv kysor-linux-x64 kysor
cp kysor /usr/local/bin/kysor
chmod +x /usr/local/bin/kysor
sudo rm -rf kysor-linux-x64.zip
```

### Initialize Kysor
```bash
kysor init --network korellia --auto-download-binaries
```

> arweave.json upload to the server in the user's home directory /root/, you can use [winscp](https://winscp.net/eng/download.php), [mobaxterm](https://mobaxterm.mobatek.net/download-home-edition.html)  or [filezilla](https://filezilla.ru/) to upload the file to the server.

### A valaccount must be created for each pool you run
> You can find the .toml files in the kysor home directory at ```$HOME/.kysor/valaccounts/```. There you can view the configuration of your valaccount.
```bash
./kysor valaccounts create \
--name moonbeam \
--pool 0 \
--storage-priv "$(cat /root/arweave.json)" \
--verbose \
--metrics
```
### Creating service file
```bash
echo "[Unit]
Description=Protocol node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/kysor start --valaccount moonbeam
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/moonbeamd.service
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

### Run node in Moonbeam pool and watch logs
> In logs you will see your ```Valladdress``` and ```Valname```. Using these data you need to authorize yourself [app.kyve.network](https://app.kyve.network/#/pools)
```bash
sudo systemctl daemon-reload
sudo systemctl enable moonbeamd
sudo systemctl restart moonbeamd
journalctl -u moonbeamd -f -o cat


sudo systemctl stop moonbeamd
```
> You need to change the port --metrics ```$HOME/.kysor/valaccounts/moonbeam.toml``` before running in the next pool
```bash
cd .kysor/valaccounts/
nano moonbeam.toml

# For example 8081 etc, save changes and restart

sudo systemctl restart moonbeamd
journalctl -u moonbeamd -f -o cat
```

### Create a valaccount new pool
```bash
./kysor valaccounts create \
--name avalanche \
--pool 1 \
--storage-priv "$(cat /root/arweave.json)" \
--verbose \
--metrics
```
### Creating service file
```bash
echo "[Unit]
Description=Protocol node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/kysor start --valaccount avalanche
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/avalanched.service
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

### Run node in Avalanche pool and watch logs.
> In logs you will see your ```Valladdress``` and ```Valname```. Using these data you need to authorize yourself [app.kyve.network](https://app.kyve.network/#/pools) 
```bash
sudo systemctl daemon-reload
sudo systemctl enable avalanched
sudo systemctl restart avalanched
journalctl -u avalanched -f -o cat


sudo systemctl stop avalanched
```
> You need to change the port --metrics ```$HOME/.kysor/valaccounts/avalanche.toml``` before running in the next pool
```bash
cd .kysor/valaccounts/
nano avalanche.toml

# For example 8082 etc, save changes and restart

sudo systemctl restart avalanched
journalctl -u avalanched -f -o cat
```

### To run in Uniswap pool, you will need 
```"INFURA_KEY"``` - [www.infura.io](https://www.infura.io/)

```"ALCHEMY_KEY"``` - [www.alchemy.com](https://www.alchemy.com/)

### Create a valaccount new pool
```bash
./kysor valaccounts create \
--name uniswap \
--pool 22 \
--storage-priv "$(cat /root/arweave.json)" \
--verbose \
--metrics
```
### Creating service file
```bash
echo "[Unit]
Description=Protocol node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/kysor start --valaccount uniswap
Restart=on-failure
LimitNOFILE=65535
Environment='API_KEYS={"https://mainnet.infura.io/v3/":"INFURA_KEY","https://eth-mainnet.g.alchemy.com/v2/":"ALCHEMY_KEY"}'

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/uniswapd.service
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

### Run node in Uniswap pool and watch logs.
> In logs you will see your ```Valladdress``` and ```Valname```. Using these data you need to authorize yourself [app.kyve.network](https://app.kyve.network/#/pools)
```bash
sudo systemctl daemon-reload
sudo systemctl enable uniswapd
sudo systemctl restart uniswapd
journalctl -u uniswapd -f -o cat


sudo systemctl stop uniswapd
```

> You need to change the port --metrics ```$HOME/.kysor/valaccounts/uniswap.toml``` before running in the next pool
```bash
cd .kysor/valaccounts/
nano uniswap.toml

# For example 8083 etc, save changes and restart

sudo systemctl restart uniswap
journalctl -u uniswapd -f -o cat
```

### To run in Axelar pool, you will need Bundlr wallet

> Setting up a Bundlr wallet
```bash
npm install -g @bundlr-network/client
```

> In order to fund the bundlr node simply execute the following, where arweave.json is your Arweave keyfile which holds some funds:
```bash
bundlr fund 1000000000000 -h https://node1.bundlr.network -w arweave.json -c arweave
```

> In this example we funded Bundlr with 1 $AR which should be more than enough. After about ~30 mins you can view your balance with:
```bash
bundlr balance address -h https://node1.bundlr.network -c arweave
```

> In order to withdraw your funds from Bundlr simply execute:
```bash
bundlr withdraw 500000000000 -h https://node1.bundlr.network -w arweave.json -c arweave
```

### Create a valaccount new pool
```bash
./kysor valaccounts create \
--name axelar \
--pool 21 \
--storage-priv "$(cat /root/arweave.json)" \
--verbose \
--metrics
```
### Creating service file
```bash
echo "[Unit]
Description=Protocol node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/kysor start --valaccount axelar
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/axelard.service
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

### Run node in Uniswap pool and watch logs.
> In logs you will see your ```Valladdress``` and ```Valname```. Using these data you need to authorize yourself [app.kyve.network](https://app.kyve.network/#/pools)
```bash
sudo systemctl daemon-reload
sudo systemctl enable axelard
sudo systemctl restart axelard
journalctl -u axelard -f -o cat


sudo systemctl stop axelard
```
> You need to change the port --metrics ```$HOME/.kysor/valaccounts/uniswap.toml``` before running in the next pool

### Create a valaccount new pool
```bash
./kysor valaccounts create \
--name bnb \
--pool 23 \
--storage-priv "$(cat /root/arweave.json)" \
--verbose \
--metrics
```
### Creating service file
```bash
echo "[Unit]
Description=Protocol node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/kysor start --valaccount bnb
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/bnbd.service
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

### Run node in BNB pool and watch logs.
> In logs you will see your ```Valladdress``` and ```Valname```. Using these data you need to authorize yourself [app.kyve.network](https://app.kyve.network/#/pools) 
```bash
sudo systemctl daemon-reload
sudo systemctl enable bnbdd
sudo systemctl restart bnbdd
journalctl -u bnbdd -f -o cat


sudo systemctl stop bnbd
```
> You need to change the port --metrics ```$HOME/.kysor/valaccounts/bnb.toml``` before running in the next pool
```bash
cd .kysor/valaccounts/
nano avalanche.toml

# For example 8182 etc, save changes and restart

sudo systemctl restart bnbd
journalctl -u bnbd -f -o cat
```
