## Snapshot Defund orbit-alpha-1
### Updated every 24 hours

```bash
#pruning 100/0/10

sudo systemctl stop defund || sudo systemctl stop defundd

defundd tendermint unsafe-reset-all --home $HOME/.defund --keep-addr-book

wget http://5.161.195.113:8000/defund-archive.tar.gz

wget -O $HOME/.defund/config/addrbook.json https://raw.githubusercontent.com/88Mikhail88/My_Testnets/main/Defund/Defund%20orbit-alpha-1/addrbook.json

tar -zxvf defund-archive.tar.gz --strip-components 1

sudo rm -rf defund-archive.tar.gz

sudo systemctl start defund || sudo systemctl restart defundd
sudo journalctl -u defund -f -o cat || sudo journalctl -u defundd -f -o cat
```
