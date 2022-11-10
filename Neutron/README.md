## Install Neutron node

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
cd $HOME
git clone https://github.com/neutron-org/neutron
cd neutron
git checkout v0.1.0
make install
cd $HOME
```
### Initialize node and download genesis:
> Make up your own name for the node and replace it with <<node_name>>
```bash
neutrond init <<node_name>> --chain-id quark-1
wget -qO $HOME/.neutrond/config/genesis.json "https://raw.githubusercontent.com/neutron-org/testnets/main/quark/genesis.json"
wget -O $HOME/.neutrond/config/addrbook.json https://github.com/88Mikhail88/My_Testnets/blob/main/Neutron/addrbook.json
```
### Adding seeds and peers
```bash
seeds="e2c07e8e6e808fb36cca0fc580e31216772841df@seed-1.quark.ntrn.info:26656,c89b8316f006075ad6ae37349220dd56796b92fa@tenderseed.ccvalidators.com:29001"
peers="fcde59cbba742b86de260730d54daa60467c91a5@23.109.158.180:26656,5bdc67a5d5219aeda3c743e04fdcd72dcb150ba3@65.109.31.114:2480,3e9656706c94ae8b11596e53656c80cf092abe5d@65.21.250.197:46656,9cb73281f6774e42176905e548c134fc45bbe579@162.55.134.54:26656,27b07238cf2ea76acabd5d84d396d447d72aa01b@65.109.54.15:51656,f10c2cb08f82225a7ef2367709e8ac427d61d1b5@57.128.144.247:26656,20b4f9207cdc9d0310399f848f057621f7251846@222.106.187.13:40006,5019864f233cee00f3a6974d9ccaac65caa83807@162.19.31.150:55256,2144ce0e9e08b2a30c132fbde52101b753df788d@194.163.168.99:26656,b37326e3acd60d4e0ea2e3223d00633605fb4f79@nebula.p2p.org:26656"
sed -i.default "s/^seeds *=.*/seeds = \"$seeds\"/;" $HOME/.neutrond/config/config.toml
sed -i "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/;" $HOME/.neutrond/config/config.toml
```
### Config pruning
```bash
recent=100
every=0
interval=10

sed -i.back "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.neutrond/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"$recent\"/g" $HOME/.neutrond/config/app.toml
sed -i "s/pruning-keep-every *=.*/pruning-keep-every = \"$every\"/g" $HOME/.neutrond/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"$interval\"/g" $HOME/.neutrond/config/app.toml
```
### Creating service file
```bash
echo "[Unit]
Description=neutrond
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which neutrond) start --home $HOME/.neutrond
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/neutrond.service
sudo mv $HOME/neutrond.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```
### Start servise
```bash
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable neutrond 

sudo systemctl restart neutrond
```

### Create a wallet, save mnemonics
> Think of a name for your wallet and replace it with <<wallet_name>>
```bash
neutrond keys add <<wallet_name>>
```
> (Optional) Recovering a wallet using mnemonics:
```bash
neutrond keys add <<wallet_name>> --recover
```
> Check if the node is synchronized, if the result is false - then the node is synchronized:
```bash
curl -s localhost:26657/status | jq .result.sync_info.catching_up
```
### Check your wallet balance
```bash
neutrond q bank balances <<address>>
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue

### Creating a validator (Replace: <<node_name>>, <<wallet_name>>) 
```bash
neutrond tx staking create-validator \
--moniker="<<node_name>>" \
--amount=1000000untrn \
--pubkey=$(neutrond tendermint show-validator) \
--chain-id=quark-1 \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.10 \
--min-self-delegation=1 \
--from=<<wallet_name>> \
--yes 
```
## Don't forget to buckup *priv_validator_key.json* file  

>- [Neutron monitoring | alerting](https://github.com/88Mikhail88/My_Testnets/blob/main/Neutron/Neutron%20monitoring%20%7C%20alerting.md)
>- [Useful CLI commands in sosmos sdk](https://github.com/88Mikhail88/My_Testnets/blob/main/Neutron/CLI%20commands%20in%20Cosmos%20sdk.md)
