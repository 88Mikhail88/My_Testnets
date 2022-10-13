# State Sync Gaia

```
# stop node
sudo systemctl stop gaiad
gaiad tendermint unsafe-reset-all --home $HOME/.gaia


SEEDS=""
PEERS="bd7dedbf3c7dc5ab33f0209dbe0151a66de3b544@5.161.153.150:23656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.gaia/config/config.toml

SNAP_RPC="http://5.161.153.150:23657"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.gaia/config/config.toml

# start node
sudo systemctl restart gaiad

# check logs
journalctl -u gaiad -f -o cat

# disable State Sync after node synchronization
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.gaia/config/config.toml
```

## Pruning config

```
recent=100
every=0
interval=10

sed -i.back "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.gaia/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"$recent\"/g" $HOME/.gaia/config/app.toml
sed -i "s/pruning-keep-every *=.*/pruning-keep-every = \"$every\"/g" $HOME/.gaia/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"$interval\"/g" $HOME/.gaia/config/app.toml
```
