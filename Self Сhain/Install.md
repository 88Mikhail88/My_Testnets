## Install Self Сhain node

### Update packages, install dependencies
```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
```
### Install go
```bash
cd $HOME
wget -O go.tar.gz https://go.dev/dl/go$VERSION.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go.tar.gz && rm go.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```
### Download and build binaries
```bash
cd $HOME
wget https://share.utsa.tech/selfchain/selfchaind
chmod +x selfchaind
mv selfchaind /usr/local/bin/selfchaind
```
### Initialize node and download genesis:
> Make up your own name for the node and replace it with <<node_name>>
```bash
selfchaind init <<node_name>> --chain-id self-dev-1
selfchaind config chain-id self-dev-1

wget -O $HOME/.selfchain/config/genesis.json "https://raw.githubusercontent.com/hotcrosscom/selfchain-genesis/main/networks/devnet/genesis.json"
```

### Config pruning
```bash
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.selfchain/config/app.toml
```
### Creating service file
```bash
echo "[Unit]
Description=selfchain
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which selfchaind) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/selfchaind.service
sudo mv $HOME/selfchaind.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```
### Start servise
```bash
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable selfchaind 

sudo systemctl restart selfchaind
```

### Create a wallet, save mnemonics
> Think of a name for your wallet and replace it with <<wallet_name>>
```bash
selfchaind keys add <<wallet_name>>
```
> (Optional) Recovering a wallet using mnemonics:
```bash
selfchaind keys add <<wallet_name>> --recover
```
> Check if the node is synchronized, if the result is false - then the node is synchronized:
```bash
curl -s localhost:26657/status | jq .result.sync_info.catching_up
```

### Check your wallet balance
```bash
selfchaind q bank balances <<address>>
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue

### Creating a validator (Replace: <<node_name>>, <<wallet_name>>) 
```bash
selfchaind tx staking create-validator \
  --amount=1000000uself \
  --pubkey=$(selfchaind tendermint show-validator) \
  --moniker="<<node_name>>" \
  --details="" \
  --identity="" \
  --website="" \
  --chain-id="self-dev-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.50" \
  --commission-max-change-rate="0.2" \
  --min-self-delegation="1" \
  --from=<<wallet_name>>
  -y
```
