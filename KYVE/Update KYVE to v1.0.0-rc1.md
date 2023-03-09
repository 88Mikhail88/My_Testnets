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
systemctl stop kyved
rm -rf /usr/local/bin/kyved

wget https://github.com/KYVENetwork/chain/releases/download/v1.0.0-rc1/kyved_linux_amd64.tar.gz
tar -xvzf kyved_linux_amd64.tar.gz
chmod +x kyved
sudo mv kyved /usr/local/bin/kyved
rm -rf kyved_linux_amd64.tar.gz

kyved version --long | head

# version: v1.0.0-rc1
# commit: cedc217027c2b7e3d2b7077abe7a4fa4442a19c4

systemctl restart kyved && journalctl -u kyved -f -o cat
```
