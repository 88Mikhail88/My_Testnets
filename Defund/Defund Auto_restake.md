### Change:
> $password  $valoper_address  $wallet_name 
>
> sleep 300s

```bash
#!/bin/bash

while :
do
	echo $password | defundd tx distribution withdraw-rewards $valoper_address --from $wallet_name --commission --chain-id=$chain_id -y

	sleep 20s

	Available=$(defundd query bank balances $address --output json | jq -r '.balances | map(select(.denom == "ufetf")) | .[].amount' | tr -cd [:digit:])
	Fees=10000
	Amount=$(($Available - $Fees))
	Amount_final=$Amount"ufetf"


	echo $password | defundd tx staking delegate $valoper_address $Amount_final --from $wallet_name --chain-id=$chain_id -y
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
