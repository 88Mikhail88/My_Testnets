## UPD Defund defund-private-3

```bash
systemctl stop defundd

defundd tendermint unsafe-reset-all --home $HOME/.defund

wget -O $HOME/.defund/config/defund-private-3-gensis.tar.gz "https://raw.githubusercontent.com/defund-labs/testnet/main/defund-private-3/defund-private-3-gensis.tar.gz"

rm -rf $HOME/.defund/config/genesis.json

cd $HOME/.defund/config/

tar -xzvf defund-private-3-gensis.tar.gz

rm -rf defund-private-3-gensis.tar.gz

cd $HOME

defundd config chain-id defund-private-3

systemctl restart defundd

journalctl -u defundd -f -o cat
```
### You should see

![](https://github.com/88Mikhail88/My_Images/blob/main/Defund/Screenshot_21.png)
