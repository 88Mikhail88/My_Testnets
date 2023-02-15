## Install KYVE ```chain-id Kaon-1```

### Update packages, install dependencies
```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
```
### Install go
```bash
cd $HOME
wget -O go1.19.1.linux-amd64.tar.gz https://golang.org/dl/go1.19.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.1.linux-amd64.tar.gz && rm go1.19.1.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```
### Download and build binaries
```bash
wget https://files.kyve.network/chain/v1.0.0-rc0/kyved_linux_amd64.tar.gz
tar -xvzf kyved_linux_amd64.tar.gz
chmod +x kyved
sudo mv kyved /usr/local/bin/kyved
rm kyved_linux_amd64.tar.gz
```
### Initialize node and download genesis:
> Make up your own name for the node and replace it with <<node_name>>
```bash
kyved init <<node_name>> --chain-id kaon-1
wget https://raw.githubusercontent.com/KYVENetwork/networks/main/kaon-1/genesis.json
mv genesis.json ~/.kyve/config/genesis.json
wget -O $HOME/.kyve/config/addrbook.json https://raw.githubusercontent.com/88Mikhail88/My_Testnets/main/KYVE/addrbook.json
```
### Adding seeds and peers
```bash
peers="7820d73c4449e0e4328c9fc4437b00aef8de33c2@5.161.195.113:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.kyve/config/config.toml
```
### Config pruning
```bash
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"

sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/.kyve/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/.kyve/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/.kyve/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/.kyve/config/app.toml
```
### Creating service file
```bash
echo "[Unit]
Description=Kyve
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/kyved start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/kyved.service
sudo mv $HOME/kyved.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```
### Start servise
```bash
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable kyved 

sudo systemctl restart kyved
```

### Create a wallet, save mnemonics
> Think of a name for your wallet and replace it with <<wallet_name>>
```bash
kyved keys add <<wallet_name>>
```
> (Optional) Recovering a wallet using mnemonics:
```bash
kyved keys add <<wallet_name>> --recover
```
> Check if the node is synchronized, if the result is false - then the node is synchronized:
```bash
curl -s localhost:26657/status | jq .result.sync_info.catching_up
```

### Check your wallet balance
```bash
kyved q bank balances <<address>>
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue
