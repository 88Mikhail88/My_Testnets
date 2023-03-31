## Install Massa node TEST.20.2
##### Scoring will start on March 24, 11:06am UTC. Scoring will end on March 29, 8:14pm UTC

### Update packages, install dependencies
```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
```
### Download and unpacking binaries
```bash
cd $HOME
wget https://github.com/massalabs/massa/releases/download/TEST.20.2/massa_TEST.20.2_release_linux.tar.gz
tar zxvf massa_TEST.20.2_release_linux.tar.gz
rm -rf massa_TEST.20.2_release_linux.tar.gz
```
### Add ip in config.toml
```bash
sudo tee <<EOF >/dev/null $HOME/massa/massa-node/config/config.toml
[network]
routable_ip = "`wget -qO- eth0.me`"
EOF
```
### Start node and come up with a password
```bash
cd $HOME/massa/massa-node/ && ./massa-node

# Stop node Ctrl+C
```
### Creating service file, change "Your_Password"
```bash
printf "[Unit]
Description=Massa
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/massa/massa-node
ExecStart=$HOME/massa/massa-node/massa-node -p Your_Password
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target" > /etc/systemd/system/massad.service
```
### Start servise
```bash
sudo systemctl daemon-reload
sudo systemctl enable massad
sudo systemctl restart massad

# check logs
sudo journalctl -f -n 100 -u massad
```
### Launch client and come up with a password
```bash
cd $HOME/massa/massa-client/
./massa-client
wallet_generate_secret_key
```
### Registering a wallet address for steaking
```bash
wallet_info
node_start_staking your_wallet_address

# check out staking_addresses
node_get_staking_addresses

# exit client by Ctrl+C

# check node
cd /$HOME/massa/massa-client/ && ./massa-client wallet_info
```
### Request test tokens in discord [#⌠💸⌡testnet-faucet](https://discord.com/channels/828270821042159636/866190913030193172)
```bash
# check balance | change Your_Password

cd /$HOME/massa/massa-client/ && ./massa-client -p Your_Password wallet_info

# buy_rolls | change your_wallet_address

buy_rolls your_wallet_address 1 0

# check roll

wallet_info

# Wait 1 hour and 40 minutes for the roll to become active and for the coins to start staking.
```
### Register node in MassaBot discord:

Send the server IP to bot and get the id
```bash
# Launching client | change Your_Password
cd /$HOME/massa/massa-client/ && ./massa-client -p Your_Password

# running the command | change your_wallet_address and your_id
node_testnet_rewards_program_ownership_proof your_wallet_address your_id

# In response, you will receive a long key, which must be copied and pasted into the MassaBot discord

# request status | change Your_Password
cd /$HOME/massa/massa-client/ && ./massa-client -p Your_Password get_status

# request status in MassaBot discord
info
```

### Delete Massa node
```bash
sudo systemctl stop massad
rm -rf $HOME/massa
rm -rf /etc/systemd/system/massad.service
rm -rf /etc/systemd/system/multi-user.target.wants/massad.service
```

![](https://github.com/88Mikhail88/My_Images/blob/main/Massa/Screenshot_10.png)
![](https://github.com/88Mikhail88/My_Images/blob/main/Massa/Screenshot_11.png)
