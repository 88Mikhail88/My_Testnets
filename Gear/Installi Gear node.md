## Gear node

```bash
sudo apt update
sudo apt install htop mc curl tar wget jqgit make ncdu jq chrony net-tools iotop nload -y

wget --no-check-certificate https://get.gear.rs/gear-nightly-linux-x86_64.tar.xz
tar -xvzf gear-nightly-linux-x86_64.tar.xz
chmod +x gear
mv gear /usr/local/bin/gear
rm -rf gear-nightly-linux-x86_64.tar.xz
```
#### Creat service, edit ```"NODE_NAME"```
```bash
echo "[Unit]
Description=Gear
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/gear --name "NODE_NAME" --telemetry-url "ws://telemetry-backend-shard.gear-tech.io:32001/submit 0"
Restart=on-failure
LimitNOFILE=10000

[Install]
WantedBy=multi-user.target" > $HOME/gear-node.service

sudo mv $HOME/gear-node.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF

sudo systemctl daemon-reload
sudo systemctl enable gear-node
sudo systemctl restart gear-node

sudo journalctl -u gear-node -f -o cat
```
#### [Telemetry](https://telemetry.gear-tech.io/)

#### Remove node
```bash
sudo systemctl stop gear-node
sudo systemctl disable gear-node
sudo rm -rf /root/.local/share/gear
sudo rm /etc/systemd/system/gear-node.service
sudo rm /usr/local/bin/gear
```
#### [Backup and restore node](https://wiki.gear-tech.io/docs/node/backup-restore)
