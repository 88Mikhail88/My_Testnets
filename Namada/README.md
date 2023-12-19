# Install Namada
```bash
# Set the desired version:

NAMADA_TAG="v0.23.1"

# Download and extract:

curl -L -o namada.tar.gz "https://github.com/anoma/namada/releases/download/$NAMADA_TAG/namada-${NAMADA_TAG}-Linux-x86_64.tar.gz"
tar -xvf namada.tar.gz

sudo mv namada-${NAMADA_TAG}-Linux-x86_64/* /usr/local/bin/

rm -rf namada-${NAMADA_TAG}-Linux-x86_64 namada.tar.gz

# version

namada --version

# Install Protocol Buffers

PROTOBUF_TAG="v24.4"

curl -L -o protobuf.zip "https://github.com/protocolbuffers/protobuf/releases/download/$PROTOBUF_TAG/protoc-${PROTOBUF_TAG#v}-linux-x86_64.zip"
mkdir protobuf_temp && unzip protobuf.zip -d protobuf_temp/

sudo cp protobuf_temp/bin/protoc /usr/local/bin/
sudo cp -r protobuf_temp/include/* /usr/local/include/

rm -rf protobuf_temp protobuf.zip

# version

protoc --version
# Install CometBFT

COMETBFT_TAG="v0.37.2"

curl -L -o cometbft.tar.gz "https://github.com/cometbft/cometbft/releases/download/$COMETBFT_TAG/cometbft_${COMETBFT_TAG#v}_linux_amd64.tar.gz"
mkdir cometbft_temp && tar -xvf cometbft.tar.gz -C cometbft_temp/

sudo mv cometbft_temp/cometbft /usr/local/bin/

rm -rf cometbft_temp cometbft.tar.gz

# version
cometbft --version

mkdir -p $HOME/.local/share/namada/pre-genesis/

CHAIN_ID=public-testnet-14.5d79b6958580
echo "export CHAIN_ID=$CHAIN_ID" >> ~/.bashrc

# For Pre-Genesis Validators

ALIAS=$(basename $(ls -d $HOME/.local/share/namada/pre-genesis/*/) | head -n 1)
echo "export ALIAS=$ALIAS" >> ~/.bashrc

# Join the network as a validator:

namada client utils join-network --chain-id $CHAIN_ID --genesis-validator $ALIAS

# For Full Nodes & Intending Post-Genesis Validators

namada client utils join-network --chain-id $CHAIN_ID

sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Environment=TM_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
ExecStart=/usr/local/bin/namada node ledger run 
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

# Reload the Systemd Configuration:

sudo systemctl daemon-reload

sudo systemctl enable namadad
sudo systemctl restart namadad

sudo journalctl -u namadad -f -o cat

# Additional Steps for Post-Genesis Validators

curl -s localhost:26657/status | grep "catching_up"

ALIAS=<your-validator-alias-here>
echo "export ALIAS=$ALIAS" >> ~/.bashrc

namada wallet address gen --alias $ALIAS

namada client init-validator \
  --alias $ALIAS \
  --account-keys $ALIAS \
  --signing-keys $ALIAS \
  --commission-rate <enter-your-commission-rate> \
  --max-commission-rate-change <enter-decimal-rate>
```
