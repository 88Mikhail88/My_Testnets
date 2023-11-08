### Aleo badge
```bash
sudo apt update && sudo apt upgrade -y 

sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool unzip -y
```

```bash
wget https://github.com/AleoHQ/leo/releases/download/v1.10.0/leo-v1.10.0-x86_64-unknown-linux-musl.zip
unzip leo-v1.10.0-x86_64-unknown-linux-musl.zip
mv leo /usr/local/bin/
leo account new
```
```bash
leo example tictactoe
cd tictactoe
leo run new

git init && git symbolic-ref HEAD refs/heads/main
git add README.md build inputs program.json src
```

```bash
git config --global user.email "user.email.github"
git config --global user.name "user.name.github"
git commit -m "First commit"
```

##### Create a repository, copy the path https://github.com/new 

```bash
git remote add origin <PATH_NEW_REPO>
git remote -v
git push -u origin main
```

##### Edit README.md and add your discord
Discord `discord`

##### Let's go and create according to the model https://github.com/AleoHQ/leo/issues
##### We put a star on the repository

```bash
# add commit
@all-contributors please add @NAME for Tutorials.
```

##### delete
```bash
cd && rm -rf leo-v1.10.0-x86_64-unknown-linux-musl.zip tictactoe
```
