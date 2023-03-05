## Update Defund to v0.2.5
### Software Upgrade [Proposal #4](https://defund.explorers.guru/proposal/4)

|Software Upgrade | Details|
|-----------------|--------|
|Name | [v0.2.5](https://github.com/defund-labs/defund/releases/tag/v0.2.5) |
|Time | 0001-01-01T00:00:00Z |
|Height | [6 108 687](https://defund.explorers.guru/block/6108687) |
|Upgraded Client State | null |

### Manual Upgrade
```bash
sudo systemctl stop defund

cd
rm -rf defund
git clone https://github.com/defund-labs/defund.git
cd defund
git checkout v0.2.5
make build
sudo mv ./build/defundd /usr/local/bin/
sudo systemctl start defund
journalctl -u defund -f -o cat
```
