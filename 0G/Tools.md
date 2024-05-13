# Wallet
```bash
# Create/recover wallet

0gchaind keys add wallet

0gchaind keys add walle --recover
```
# Validator

```bash
#Create Validator

0gchaind tx staking create-validator \
  --amount=1000000ua0gi \
  --pubkey=$(0gchaind tendermint show-validator) \
  --moniker="Your-Moniker" \
  --chain-id=zgtendermint_16600-1 \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --from=wallet \
  --gas=auto \
  --gas-adjustment=1.4
```
