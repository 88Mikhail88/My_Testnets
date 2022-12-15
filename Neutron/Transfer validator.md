## Transferring validator to another server

### Run a new node on a new server

### Download priv_validator_key.json from the old server to your computer (You must have the mnemonic from the wallet saved)
```bash
~/.$cli/config/priv_validator_key.json
```
#### To check synchronization status
```bash
curl -s localhost:26657/status | jq .result.sync_info
```
### After the node on new server is synchronized, restore wallet using mnemonics
```bash
$cli keys add <<wallet_name>> --recover
```
### Stop nodes (new and old server)
```bash
sudo systemctl stop $cli
```
### Remove priv_validator_key.json on old server
```bash
cd ~/.$cli/config/priv_validator_key.json && rm -rf priv_validator_key.json
```
### Transfer priv_validator_key.json to new server
> you can use [winscp](https://winscp.net/eng/download.php), [mobaxterm](https://mobaxterm.mobatek.net/download-home-edition.html) or [filezilla](https://filezilla.ru/)

### Start service on a new server
```bash
sudo systemctl start $cli
```
### Make sure your validator is not jailed
> To unjail your validator
```bash
$cli tx slashing unjail --chain-id $chain-id --from <<wallet_name>> --gas=auto -y
```

### You can delete the old server completely to eliminate double signatures
