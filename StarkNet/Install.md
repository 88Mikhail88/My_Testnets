### StarkNet node

#### [Alchemy](https://alchemy.com/?r=cf7a724f05e2bf08)

```bash
ALCHEMY=your_alchemy_http_address
echo 'export ALCHEMY='$ALCHEMY >> $HOME/.bash_profile
```


```bash
sudo apt update
sudo apt-get install software-properties-common -y
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install curl git tmux build-essential libgmp-dev pkg-config libssl-dev libzstd-dev protobuf-compiler -y
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
```

```bash
source $HOME/.cargo/env
rustup update stable --force
```

```bash
mkdir -p $HOME/.starknet/db
cd $HOME
rm -rf pathfinder
git clone https://github.com/eqlabs/pathfinder.git
cd pathfinder
git fetch
git checkout v0.9.5
```

```bash
cargo build --release --bin pathfinder
sleep 2
source $HOME/.bash_profile
sudo mv ~/pathfinder/target/release/pathfinder /usr/local/bin/ || exit
```

```bash
echo "[Unit]
Description=StarkNet
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/pathfinder --http-rpc=\"0.0.0.0:9545\" --ethereum.url \"$ALCHEMY\" --data-directory \"$HOME/.starknet/db\"
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/starknetd.service
```

```bash
sudo mv $HOME/starknetd.service /etc/systemd/system/
sudo systemctl restart systemd-journald

sudo systemctl daemon-reload
sudo systemctl enable starknetd
sudo systemctl restart starknetd
```



#### Additional

```bash
# Check logs:

journalctl -u starknetd -f

# Restart node:

systemctl restart starknetd

# Delete node:

systemctl stop starknetd
systemctl disable starknetd
rm -rf ~/pathfinder/
rm -rf /etc/systemd/system/starknetd.service
rm -rf /usr/local/bin/pathfinder
```
