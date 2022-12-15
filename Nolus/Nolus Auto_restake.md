### Change:
> $password  $valoper_address  $wallet_name  $address
>
> sleep 21600s

```bash
#!/bin/bash

while :
do
	echo $password | nolusd tx distribution withdraw-rewards $valoper_address --from $wallet_name --chain-id nolus-rila --commission --fees 500unls -y

	sleep 20s

	AVAILABLE_COIN=$(nolusd query bank balances $address --output json | jq -r '.balances | map(select(.denom == "unls")) | .[].amount' | tr -cd [:digit:])
	KEEP_FOR_FEES=100000
	AMOUNT=$(($AVAILABLE_COIN - $KEEP_FOR_FEES))
	AMOUNT_FINAL=$AMOUNT"unls"


	
	echo MA08121988 | nolusd tx staking delegate $valoper_address "${AMOUNT_FINAL}" --from $wallet_name --chain-id nolus-rila --fees 500unls -y
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
