## Install Ojo node

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
git clone https://github.com/ojo-network/ojo && cd ojo
git checkout v0.1.2
make install
cd $HOME

ojod version --long
# HEAD-ad5a2377134aa13d7d76575b95613cf8ed12d1e4
# commit: ad5a2377134aa13d7d76575b95613cf8ed12d1e4
```
### Initialize node and download genesis:
> Make up your own name for the node and replace it with <<node_name>>
```bash
ojod init <<node_name>> --chain-id ojo-devnet

wget -O $HOME/.ojo/config/genesis.json https://raw.githubusercontent.com/88Mikhail88/My_Testnets/main/Ojo/genesis.json
wget -O $HOME/.ojo/config/addrbook.json https://raw.githubusercontent.com/88Mikhail88/My_Testnets/main/Ojo/addrbook.json
```
### Adding peers
```bash
peers="17a5fad48064ee3da42f435925f7bbe055e6348d@ojo-testnet.nodejumper.io:37656,239caa37cb0f131b01be8151631b649dc700cd97@95.217.200.36:46656,b133dde2713a216a017399920419fcb1e084cdb2@136.243.88.91:7330,2c40b0aedc41b7c1b20c7c243dd5edd698428c41@138.201.85.176:26696,7d6706d7ee674e2b2c38d3eb47d85ec6e376c377@49.12.123.87:56656,9ebe723eef929e9eff748f4046d6130ee349a398@65.108.203.149:24017,8fbfa810cb666ddef1c9f4405e933ef49138f35a@65.108.199.120:54656,0d4dc8d9e80df99fdf7fbb0e44fbe55e0f8dde28@65.108.205.47:14756,8b6c75d20ac3ceeb7f0f1d4b5fc89a69e567c47b@65.108.231.238:36656,7bf4e4a18bf2006f79f50c79903f77d4e2a5a303@65.21.77.175:33307,3147dcfa5c320f31b083a1d1319cbcb010108f9f@65.108.233.220:17656,bdd24cab3246503ae261aea82f077ffb66d56ce3@95.216.39.183:28656,d6318facf0de085644dcf8ba57bcc1725b6ec515@89.58.59.75:36656,406466cf9c390c0fd1784a197f2bb38a786da35e@185.111.159.115:28656,d5519e378247dfb61dfe90652d1fe3e2b3005a5b@65.109.68.190:50656,7416a65de3cc548a537dbb8bdf93dbd83fe401d2@78.107.234.44:26656,2720b3b9e1a318d5b4abdad65714c55e09f43965@82.208.21.78:36656,f702b19a4dae5ad813dabe3f529bf31c160a74e0@5.189.176.202:26656,f474a520009496972515f843cdb835fc7d663779@65.109.23.114:21656,1879aa588b4d6431bf40543f3a44129dcf60a043@144.91.77.68:50656,e052b7c899bae41f6d89f70f81de50e28b72a7bf@38.242.237.100:26656,f4663c5df8ee2e2b6e1cc6a9d7ad09687a27e08c@68.183.32.158:26656,da369d44c00dba309237b21391806504353d188f@194.163.187.175:50656,c37e444f67af17545393ad16930cd68dc7e3fd08@95.216.7.169:61156,9ea0473b3684dbf1f2cf194f69f746566dab6760@78.46.99.50:22656,3130147135ce803784e0941aecbcc2597bc74425@85.214.116.96:33656,d1c5c6bf4641d1800e931af6858275f08c20706d@23.88.5.169:18656,b6c75d1fbdc9c39daaaf52a4c0937b9f06975808@167.235.198.193:26656,f12af93f4f59534a022192408c31fdd1d2f1bb0c@38.242.131.92:26656,b6b4a4c720c4b4a191f0c5583cc298b545c330df@65.109.28.219:21656,67c653cec3e0e116939841b9c601b43daecae47e@116.202.170.159:24656,11bb322f6396a1ca67717cf162385ed250503e28@154.12.253.123:36656,1b5c5927e6e3685b3e9fc278ca4c9d7002d4cc10@65.21.134.250:17656,855fc154f9054ce4055719e09ce6f7f1d0ecd9fb@85.10.198.171:36656,18300f0a5973798c3900fe51ff255bb6bca982f9@65.109.65.248:36656,8d526cda50211b9dc370ad3bbd8972958c1157d1@91.113.222.115:26656,d97bd8a6ca6766efd8f5a5fb5af96a92a4f2fe4e@195.3.222.189:26656,7186f24ace7f4f2606f56f750c2684d387dc39ac@65.108.231.124:12656,5a4201370808de8fe5926db82767d8be44c9d288@51.222.42.89:50656,d9df87e2e26db62ef4014ce6e8705ee11bda304f@176.124.220.21:4669,da9e028814ff30ec24e94bec6887f4686f692b86@173.212.222.167:30656,50ad0e558d9da6fce98ae4527cd49ee3e8d19940@94.250.202.215:26656,cb706ebe1d7a1f1d3e281bf46a78d84251f50810@95.216.14.72:26656,8671c2dbbfd918374292e2c760704414d853f5b7@35.215.121.109:26656,1b81440d84a2746af6fba80c1a3a091f298f7a7a@185.206.214.254:26656,dd100ed6f1046f8db6d1d7ad04ed6253f935e9b2@176.118.198.128:26656,5c2a752c9b1952dbed075c56c600c3a79b58c395@95.214.52.139:27226,ae3621c022cddc8c05d7640c14147d257746fb74@185.215.166.73:26656,a23cc4cbb09108bc9af380083108262454539aeb@35.215.116.65:26656,b0968b57bcb5e527230ef3cfa3f65d5f1e4647dd@35.212.224.95:26656"
sed -i "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/;" $HOME/.ojo/config/config.toml
```
### Config pruning
```bash
recent=100
every=0
interval=10

sed -i.back "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.ojo/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"$recent\"/g" $HOME/.ojo/config/app.toml
sed -i "s/pruning-keep-every *=.*/pruning-keep-every = \"$every\"/g" $HOME/.ojo/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"$interval\"/g" $HOME/.ojo/config/app.toml
```
### Creating service file
```bash
echo "[Unit]
Description=Ojo
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which ojod) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/ojod.service
sudo mv $HOME/ojod.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```
### Start servise
```bash
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable ojod 

sudo systemctl restart ojod
```

### Create a wallet, save mnemonics
> Think of a name for your wallet and replace it with <<wallet_name>>
```bash
ojod keys add <<wallet_name>>
```
> (Optional) Recovering a wallet using mnemonics:
```bash
ojod keys add <<wallet_name>> --recover
```
> Check if the node is synchronized, if the result is false - then the node is synchronized:
```bash
curl -s localhost:26657/status | jq .result.sync_info.catching_up
```

### Check your wallet balance
```bash
ojod q bank balances <<address>>
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue

### Creating a validator (Replace: <<node_name>>, <<wallet_name>>) 
```bash
ojod tx staking create-validator \
--moniker="<<node_name>>" \
--amount=1000000uojo \
--pubkey=$(ojod tendermint show-validator) \
--chain-id=ojo-devnet \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.05 \
--min-self-delegation=1 \
--from=<<wallet_name>> \
--yes 
```

## Don't forget to buckup **priv_validator_key.json** file  
