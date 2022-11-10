### Change:
> $password  $valoper_address  $wallet_name  $address
>
> sleep 1200s

```bash
#!/bin/bash

while :
do
	echo $password | neutrond tx distribution withdraw-rewards $valoper_address --from $wallet_name --commission --chain-id=quark-1 -y

	sleep 20s

	Available=$(neutrond query bank balances $address --output json | jq -r '.balances | map(select(.denom == "untrn")) | .[].amount' | tr -cd [:digit:])
	Fees=1000
	Amount=$(($Available - $Fees))
	Amount_final=$Amount"untrn"


	echo $password | neutrond tx staking delegate $valoper_address $Amount_final --from $wallet_name --chain-id=quark-1 -y
	date
	sleep 1200s
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
