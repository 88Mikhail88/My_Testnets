# State Sync Haqq --chain-id haqq_54211-2

```
# stop node
sudo systemctl stop haqqd
haqqd tendermint unsafe-reset-all --home $HOME/.haqqd


SEEDS=""
PEERS="c46598b1ad703b3b15f9c9fc9748b80ad9a22723@116.203.37.162:26656"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.haqqd/config/config.toml

SNAP_RPC="http://116.203.37.162:26657"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.haqqd/config/config.toml

# start node
sudo systemctl restart haqqd

# check logs
journalctl -u haqqd -f -o cat
```

## Pruning config
```
recent=100
every=0
interval=10

sed -i.back "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.haqqd/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"$recent\"/g" $HOME/.haqqd/config/app.toml
sed -i "s/pruning-keep-every *=.*/pruning-keep-every = \"$every\"/g" $HOME/.haqqd/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"$interval\"/g" $HOME/.haqqd/config/app.toml
```

## Ports used:
```26657 | 6060 | 26656 | 9090 | 9091 ```

## Upgrade:
```
cd && rm -rf haqq
git clone https://github.com/haqq-network/haqq.git
cd haqq && git checkout v1.1.0
make build
sudo mv build/haqqd $(which haqqd)
sudo systemctl restart haqqd```
