## Update Nolus to v0.2.1-testnet
### Software Upgrade [Proposal #57](https://nolus.explorers.guru/proposal/57)

|Software Upgrade | Details|
|-----------------|--------|
|Name | [v0.2.1 Nolus Rila](https://github.com/Nolus-Protocol/nolus-core/releases/tag/v0.2.1-testnet) |
|Time | 0001-01-01T00:00:00Z |
|Height | [1 327 000](https://nolus.explorers.guru/block/1327000) |
|Upgraded Client State | null |

### Manual Upgrade
```bash
systemctl stop nolusd

cd $HOME
rm -rf nolus-core

git clone https://github.com/Nolus-Protocol/nolus-core.git
cd nolus-core
git checkout v0.2.1-testnet
make build
sudo mv target/release/nolusd /usr/local/bin/
rm -rf build

sudo systemctl restart nolusd  && sudo journalctl -u nolusd -f -o cat
```
