### Install

```bash
curl -L https://dymensionxyz.github.io/roller/install.sh | bash

# version
roller version
```

```bash
roller config init --interactive

# Select your network: Devnet (default)
# Enter your RollApp ID
# Specify your RollApp denom
# Set the genesis token supply
# Choose your data layer: Celestia (default)
# Select your execution environment: EVM (default)
```
for testnet tokens: [devnet-faucet](https://discord.com/channels/956961633165529098/1125047988247593010) [celestia-faucet](https://discord.com/channels/956961633165529098/1128048548999610451)

```bash
roller register
roller run

# example

rollapp_evm tx ibc-transfer transfer transfer <src-channel> dym-wallet 1000000000000000000<base-denom> — from rollapp_sequencer — keyring-backend test — home ~/.roller/rollapp — broadcast-mode block
```

```bash
roller keys list
roller keys export hub_sequencer
roller keys export rollapp_sequencer
roller keys export my_celes_key
```
### Connect Metamask

```bash
curl -L https://dymensionxyz.github.io/roller/install.sh | bash
roller run
```
### Import Account Metamask && Add a network manually in MM and you can create Smart contract
