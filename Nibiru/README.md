## Install Nibiru node

### Update packages, install dependencies
```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
```
### Install go
```bash
cd $HOME
wget -O go1.18.4.linux-amd64.tar.gz https://golang.org/dl/go1.18.4.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.4.linux-amd64.tar.gz && rm go1.18.4.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```
### Download and build binaries
```bash
cd $HOME
git clone https://github.com/NibiruChain/nibiru
cd nibiru
make install
cd $HOME
```
### Initialize node and download genesis:
> Make up your own name for the node and replace it with <<node_name>>
```bash
nibid init <<node_name>> --chain-id nibiru-testnet-1
curl -s https://rpc.testnet-1.nibiru.fi/genesis | jq -r .result.genesis > genesis.json
cp genesis.json $HOME/.nibid/config/genesis.json
```
### Adding peers
```bash
peers="37713248f21c37a2f022fbbb7228f02862224190@35.243.130.198:26656,ff59bff2d8b8fb6114191af7063e92a9dd637bd9@35.185.114.96:26656,cb431d789fe4c3f94873b0769cb4fce5143daf97@35.227.113.63:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nibid/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 100/g' $HOME/.nibid/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 100/g' $HOME/.nibid/config/config.toml

sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0unibi\"/;" ~/.nibid/config/app.toml
```
### Config pruning
```bash
recent=100
every=0
interval=10

sed -i.back "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.nibid/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"$recent\"/g" $HOME/.nibid/config/app.toml
sed -i "s/pruning-keep-every *=.*/pruning-keep-every = \"$every\"/g" $HOME/.nibid/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"$interval\"/g" $HOME/.nibid/config/app.toml
```
### Creating service file
```bash
echo "[Unit]
Description=nibid
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which nibid) start --home $HOME/.nibid
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/nibid.service
sudo mv $HOME/nibid.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```
### Start servise
```bash
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable nibid 

sudo systemctl restart nibid 
```

### Create a wallet, save mnemonics
> Think of a name for your wallet and replace it with <<wallet_name>>
```bash
nibid keys add <<wallet_name>>
```
> (Optional) Recovering a wallet using mnemonics:
```bash
nibid keys add <<wallet_name>> --recover
```
> Check if the node is synchronized, if the result is false - then the node is synchronized:
```bash
curl -s localhost:26657/status | jq .result.sync_info.catching_up
```
### After the node is synchronized you need to request test tokens.
> To do this, go to Faucet in discord https://discord.gg/mET8gtGh5w

### Check your wallet balance
```bash
nibid q bank balances <<address>>
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue

### Creating a validator (Replace: <<node_name>>, <<wallet_name>>) 
```bash
nibid tx staking create-validator \
--moniker="<<node_name>>" \
--amount=10000000unibi \
--pubkey=$(nibid tendermint show-validator) \
--chain-id=nibiru-testnet-1 \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.05 \
--min-self-delegation=1 \
--from=<<wallet_name>> \
--yes 
```

## Don't forget to buckup *priv_validator_key.json* file  

>- [Nibiru State Sync](https://github.com/88Mikhail88/My_Testnets/blob/main/Nibiru/Nibiru%20State%20Sync.md)
>- [Nibiru monitoring | alerting](https://github.com/88Mikhail88/My_Testnets/blob/main/Nibiru/Nibiru%20monitoring%20%7C%20alerting.md)
>- [Useful CLI commands in sosmos sdk](https://github.com/88Mikhail88/My_Testnets/blob/main/Nibiru/CLI%20commands%20in%20Cosmos%20sdk.md)
