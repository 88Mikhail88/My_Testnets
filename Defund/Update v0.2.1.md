### Update v0.2.1

```bash
cd && rm -rf defund

git clone https://github.com/defund-labs/defund.git

cd defund && git checkout v0.2.1

make build

mv $HOME/defund/build/defundd $(which defundd)

systemctl restart defund
```
