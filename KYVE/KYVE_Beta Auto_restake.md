### Change:
> $password  $cli  $valoper_address  $wallet_name 
>
> sleep 21600s

```bash
#!/bin/bash

while :
do
	echo $password | $cli tx distribution withdraw-rewards $valoper_address --from $wallet_name --commission --chain-id=kyve-beta --gas-prices 1tkyve -y

	sleep 20s

	Available=$($cli query bank balances $address --output json | jq -r '.balances | map(select(.denom == "tkyve")) | .[].amount' | tr -cd [:digit:])
	Fees=100000
	Amount=$(($Available - $Fees))
	Amount_final=$Amount"tkyve"


	echo $password | $cli tx staking delegate $valoper_address $Amount_final --from $wallet_name --chain-id=kyve-beta --gas-prices 1tkyve -y
	date
	sleep 21600s
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
