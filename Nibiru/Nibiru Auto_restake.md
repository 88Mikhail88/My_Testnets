### Change:
> $password  $cli  $valoper_address  $wallet_name 
>
> sleep 300s

```bash
#!/bin/bash

while :
do
	echo $password | nibid tx distribution withdraw-rewards $valoper_address --from $wallet_name --commission --chain-id=nibiru-testnet-1 -y

	sleep 20s

	Available=$(nibid query bank balances $address --output json | jq -r '.balances | map(select(.denom == "unibi")) | .[].amount' | tr -cd [:digit:])
	Fees=100000
	Amount=$(($Available - $Fees))
	Amount_final=$Amount"unibi"


	echo $password | nibid tx staking delegate $valoper_address $Amount_final --from $wallet_name --chain-id=nibiru-testnet-1 -y
	date
	sleep 300s
done;
```
### Create file *restake.sh* in your server, make it executable and paste cod (pre-edit)
```bash
touch restake.sh
chmod +x restake.sh
nano restake.sh
```
### Run script in a screen session
```bash
screen -S restake
./restake.sh
```
