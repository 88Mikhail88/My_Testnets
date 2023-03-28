## Taiko node

#### Docker install

```bash
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
    
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg > /dev/null

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

#### Install Taiko
[Alchemy](https://www.alchemy.com/) | [Sepolia faucet](https://sepoliafaucet.com/) | [Explorer](https://explorer.a2.taiko.xyz/)

```bash
git clone https://github.com/taikoxyz/simple-taiko-node.git

cd simple-taiko-node

cp .env.sample .env

nano .env

# Edit
L1_ENDPOINT_HTTP=https://ALCHEMY_ENDPOINT_HTTP
L1_ENDPOINT_WS=wss://ALCHEMY_ENDPOINT_WSS
ENABLE_PROVER=true (prover) or false (node)
L1_PROVER_PRIVATE_KEY=YOUR_PRIVATE_KEY_Metamask
```
#### Run node
```bash
docker compose up -d

# check logs
docker compose logs -f

# stop node
docker compose down
```
