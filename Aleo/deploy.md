### SnarkOS
```bash
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install -y

# screen session
screen -S contract

# Proceed with installation (default)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

git clone https://github.com/AleoHQ/snarkOS.git --depth 1
cd snarkOS
./build_ubuntu.sh

source $HOME/.cargo/env
cargo install --path .
```

### Leo language
```bash
cd
git clone https://github.com/AleoHQ/leo
cd leo
cargo install --path .
```
### Test tokens 
Generate wallet [Aleo](https://aleo.tools/) and copy `Private Key`, `View Key`, `Address`

![](https://github.com/88Mikhail88/My_Images/blob/main/Aleo/Screenshot_1.png)
```bash
# Edit Your_Wallet_Address Your_Privat_Key
snarkos developer execute credits.aleo mint Your_Wallet_Address 100000000u64 \
--private-key Your_Privat_Key \
--query "https://vm.aleo.org/api" \
--broadcast "https://vm.aleo.org/api/testnet3/transaction/broadcast"
```
### Install extension in browser [JSON Beautifier & Editor](https://chrome.google.com/webstore/detail/json-beautifier-editor/lpopeocbeepakdnipejhlpcmifheolpl/related)

- Add transaction number from terminal to link and paste into browser.
- Copy value
- Add `value`, `View Key` to [Aleo](https://aleo.tools/) and copy `Record`
```bash
https://vm.aleo.org/api/testnet3/transaction/at1......ynt
```

![](https://github.com/88Mikhail88/My_Images/blob/main/Aleo/Screenshot_2.png)
![](https://github.com/88Mikhail88/My_Images/blob/main/Aleo/Screenshot_3.png)
![](https://github.com/88Mikhail88/My_Images/blob/main/Aleo/Screenshot_4.png)

### Deploy app
```bash
cd $HOME
mkdir deploy && cd deploy

# Edit Your_Wallet_Address, Your_App_Name, Your_Privat_Key, Your_Record
Wallet_Address="Your_Wallet_Address"
App_Name=Your_App_Name_"${Wallet_Address:4:6}"
echo $App_Name

leo new "${App_Name}"
cd "${App_Name}" && leo run && cd -

Path_to_app=$(realpath -q $App_Name)
echo $Path_to_app

Privat_Key="Your_Privat_Key"

Record="Your_Record"
```

```bash
snarkos developer deploy "${App_Name}.aleo" \
--private-key "${Privat_Key}" \
--query "https://vm.aleo.org/api" \
--path "./${App_Name}/build/" \
--broadcast "https://vm.aleo.org/api/testnet3/transaction/broadcast" \
--fee 600000 --record "${Record}"
```

```bash
# Close screen session
ctrl+a d

# View running screen sessions
screen -ls

# Open screen session
screen -r id_session
```
