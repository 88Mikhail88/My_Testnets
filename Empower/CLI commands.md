## Useful CLI commands in Cosmos sdk

> **Values** *$cli $address $to_address $amount $chain_id $denom $node_name $wallet_name $id_prop $valoper_address $tx_hash* **replace with your !**

### Search for Information

```bash
# Check logs:
journalctl -u $cli -f -o cat

# Restart | stop | status node:
systemctl restart $cli
systemctl stop $cli
systemctl status $cli

# Check current block height:
$cli status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check if node is synchronized, if result false - node is synchronized
curl -s localhost:26657/status | jq .result.sync_info.catching_up

# Node info:
$cli status | jq

# Check tx_hash information (tx_hash will not be found if indexing is disabled):
$cli query tx $tx_hash

# Parameters:
$cli q staking params
$cli q slashing params

# List validators Active set:
$cli query staking validators --limit 2000 -o json | jq -r '.validators[] | select(.status=="BOND_STATUS_BONDED") | [.operator_address, .status, (.tokens|tonumber / pow(10; 6)), .description.moniker] | @csv' | column -t -s"," | sort -k3 -n -r

# List validators Inactive set:
$cli query staking validators --limit 2000 -o json | jq -r '.validators[] | select(.status=="BOND_STATUS_UNBONDED") | [.operator_address, .status, (.tokens|tonumber / pow(10; 6)), .description.moniker] | @csv' | column -t -s"," | sort -k3 -n -r
```

### Wallet

```bash
# Creating a Wallet (ALWAYS SAVE MNEMONIC):
$cli keys add $wallet_name

# Recovering a wallet using MNEMONIC:
$cli keys add $wallet_name --recover

# Check your wallet balance
$cli q bank balances $address 

# List of wallets:
$cli keys list

# Send tokens to another address:
$cli tx bank send $address $to_address $amount$denom --chain-id $chain_id --fees $amount$denom -y

# Show account key:
$cli keys show $wallet_name --bech val

# Show consensus key:
$cli keys show $wallet_name --bech cons

# Delete Wallet:
$cli keys delete $wallet_name
```

### Creating | editing validator 

```bash
# Create a validator:
$cli tx staking create-validator \
--moniker="$node_name" \
--amount=1000000$denom \
--fees=300$denom \
--pubkey=$($cli tendermint show-validator) \
--chain-id=$chain_id \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.10 \
--details="" \
--identity="" \
--security-contact="" \
--website "" \
--min-self-delegation=1 \
--from=$wallet_name \
--yes 

# Edit validator:
$cli tx staking edit-validator \
--details="" \
--chain-id=$chain_id \
--from=$wallet_name
--yes
```

### Validator

```bash
# Check your valoper address:
$cli keys show $wallet_name --bech val -a

# Check validator's pubkey:
$cli tendermint show-validator

# Check validator information:
$cli query staking validator $valoper_address

# Check number of blocks skipped by validator and from which block is active:
$cli q slashing signing-info $($cli tendermint show-validator)

# Find tx_hash of validator creation transaction:
$cli query txs --events='create_validator.validator=<<valoper_address>>' -o=json | jq .txs[0].txhash -r

# Delegate tokens (to increase your stake, delegate to your $valoper_address):
$cli tx staking delegate $valoper_address $amount$denom --from $wallet_name --chain-id $chain_id --fees $amount$denom -y

# Get out of jail:
$cli tx slashing unjail --chain-id $chain_id --from $wallet_name --fees $amount$denom -y

# Transfer revards and fees from validator to balance  wallet:
$cli tx distribution withdraw-rewards $valoper_address --from $wallet_name --chain-id $chain_id --commission --fees $amount$denom -y

# Transfer all delegate rewards from all validators on which you stack your tokens:
$cli tx distribution withdraw-all-rewards --chain-id $chain_id \
 --from $wallet_name --gas=auto --gas-adjustment=1.5 --fees=$amount$denom

# Unbond:
$cli tx staking unbond $($cli keys show $wallet_name --bech val -a) $amount$denom \
--from $wallet_name \
--chain-id=$chain_id \
--gas=auto --gas-adjustment=1.5 \
--fees=$amount$denom
```

### Proposls

```bash
# List proposals:
$cli q gov proposals

# Vote proposal:
$cli tx gov vote 1 yes --from $wallet_name --chain-id $chain_id --fees $amount$denom -y

# Create proposal (simple example):
$cli tx gov submit-proposal --title="Text" \
--type="Text" \
--description="Text" \
--from $wallet_name \
--chain-id=$chain_id \
--gas=auto --gas-adjustment 1.5 --fees=$amount$denom

# Make a deposit to proposal, to start voting:
$cli tx gov deposit $id_prop $amount$denom \
--from=$wallet_name \
--chain-id=$chain_id \
--gas=auto --gas-adjustment=1.5 --fees=$amount$denom
```

### Peer & Rpc

```bash
# Get your peer@ip:port:
PORTR=$(grep -A 3 "\[p2p\]" ~/.$cli/config/config.toml | egrep -o ":[0-9]+") && \
echo $($cli tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Find your RPC port:
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/.$cli/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers:
PORT=26657
curl -s http://localhost:$PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l

# List of moniker connected peers:
curl -s http://localhost:$PORT/net_info | jq '.result.peers[].node_info.moniker'

# Checking Vote Power online:
curl -s localhost:26657/consensus_state | jq '.result.round_state.height_vote_set[0].prevotes_bit_array'
```
