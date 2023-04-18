## Install Cascadia node

#### Update packages, install dependencies
```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
```
#### Install go
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
#### Download and build binaries
```bash
git clone https://github.com/cascadiafoundation/cascadia
cd cascadia
git checkout v0.1.1
make install
sudo mv ~/go/bin/cascadiad /usr/local/bin/cascadiad

cd $HOME
```
#### Initialize node, download genesis and addrbook:
> Make up your own name for the node and replace it with <<node_name>>
```bash
cascadiad <<node_name>> --chain-id cascadia_6102-1

wget -O $HOME/.cascadiad/config/genesis.json https://raw.githubusercontent.com/88Mikhail88/My_Testnets/main/Casadia/addrbook.json
wget -O $HOME/.cascadiad/config/addrbook.json https://raw.githubusercontent.com/88Mikhail88/My_Testnets/main/Casadia/addrbook.json
```
#### Config pruning
```bash
recent=100
every=0
interval=10

sed -i.back "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.cascadiad/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"$recent\"/g" $HOME/.cascadiad/config/app.toml
sed -i "s/pruning-keep-every *=.*/pruning-keep-every = \"$every\"/g" $HOME/.cascadiad/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"$interval\"/g" $HOME/.cascadiad/config/app.toml
```
#### Creating service file
```bash
echo "[Unit]
Description=cascadia
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which cascadiad) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/cascadiad.service
sudo mv $HOME/cascadiad.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```
#### Start servise
```bash
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable cascadiad 

sudo systemctl restart cascadiad
```

### Create a wallet, save mnemonics
> Think of a name for your wallet and replace it with <<wallet_name>>
```bash
cascadiad keys add <<wallet_name>>
```
> (Optional) Recovering a wallet using mnemonics:
```bash
cascadiad keys add <<wallet_name>> --recover
```
> Check if the node is synchronized, if the result is false - then the node is synchronized:
```bash
cascadiad status | jq
```
#### After the node is synchronized you need to request test tokens.
> To do this, go to [Faucet](https://www.cascadia.foundation/faucet). You need `Address (EIP-55): 0x`

check address `Address (EIP-55): 0x`
```bash
cascadiad debug addr YOUR_WALLET_ADDRESS
```

#### Check your wallet balance
```bash
cascadiad q bank balances YOUR_WALLET_ADDRESS
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue

#### Creating a validator (Replace: <<node_name>>, <<wallet_name>>) 
```bash
cascadiad tx staking create-validator \
--moniker="<<node_name>>" \
--amount=1000000000000000000aCC \
--pubkey=$(cascadiad tendermint show-validator) \
--chain-id=cascadia_6102-1 \
--commission-max-change-rate=0.01 \
--commission-max-rate=1.0 \
--commission-rate=0.05 \
--min-self-delegation="1000000" \
--from=<<wallet_name>> \
--gas auto \
--gas-adjustment=1.2 \
--gas-prices="7aCC" \
--yes 
```

## Don't forget to buckup **priv_validator_key.json** file
