## Update KYVE to v1.0.0-rc1
### Software Upgrade [Proposal #4]([https://defund.explorers.guru/proposal/4](https://kyve.explorers.guru/proposal/1))

|Software Upgrade | Details|
|-----------------|--------|
|Name | [v1.0.0-rc1](https://github.com/KYVENetwork/chain/releases/tag/v1.0.0-rc1) |
|Time | 0001-01-01T00:00:00Z |
|Height | [443 300](https://kyve.explorers.guru/block/443300) |
|Upgraded Client State | null |

### Manual Upgrade
```bash
cd $HOME
git clone https://github.com/KYVENetwork/chain/
cd chain
git checkout v1.0.0-rc1
make install
kyved version --long | head

# version: v1.0.0-rc1
# commit: cedc217027c2b7e3d2b7077abe7a4fa4442a19c4

systemctl restart kyved && journalctl -u kyved -f -o cat
```
