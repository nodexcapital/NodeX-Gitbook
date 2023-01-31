---
description: >-
  Setting up your validator node has never been so easy. Get your validator
  running in minutes by following step by step instructions.
---

# 1 Installation

<figure><img src="https://raw.githubusercontent.com/kj89/testnet_manuals/main/pingpub/logos/okp4.png" alt=""><figcaption></figcaption></figure>

**Network:** Testnet **| Chain ID:** okp4-nemeton-1 | **Version:** v3.0.0

#### Setup Validator Name / Moniker

{% hint style="info" %}
Replace **YOUR-MONIKER** with your validator name
{% endhint %}

```
MONIKER="YOUR-MONIKER"
```

#### Install dependencies

```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```

#### Install Go

```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.19.5.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

#### Download & Build Binary

```
cd $HOME
git clone https://github.com/okp4/okp4d.git
cd okp4d
git checkout v3.0.0
make build
```

#### Prepare Binaries for Cosmovisor

```
mkdir -p $HOME/.okp4d/cosmovisor/genesis/bin
mv planqd $HOME/.okp4d/cosmovisor/genesis/bin/
rm -rf build
```

#### Create Application Symlink

```
ln -s $HOME/.okp4d/cosmovisor/genesis $HOME/.okp4d/cosmovisor/current
sudo ln -s $HOME/.okp4d/cosmovisor/current/bin/okp4d /usr/local/bin/okp4d
```

#### Install Cosmovisor  & Create Service

```
#Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0

#Create Service
sudo tee /etc/systemd/system/okp4d.service > /dev/null << EOF
[Unit]
Description=okp4-testnet node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.okp4d"
Environment="DAEMON_NAME=okp4d"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable okp4d
```

#### Setup Node Configuration

```
planqd config chain-id okp4-nemeton-1
planqd config keyring-backend test
planqd config node tcp://localhost:35657

planqd init $MONIKER --chain-id okp4-nemeton-1
```

#### Download Genesis & Addrbook

```
curl -Ls https://snapshots.kjnodes.com/okp4-testnet/genesis.json > $HOME/.okp4d/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/okp4-testnet/addrbook.json > $HOME/.okp4d/config/addrbook.json
```

#### Set Gas Prices, Peers & Pruning

```
SEEDS="3f472746f46493309650e5a033076689996c8881@okp4-testnet.rpc.kjnodes.com:36659"
sed -i -e "s|^seeds *=.*|seeds = \"$SEEDS\"|" $HOME/.okp4d/config/config.toml
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0uknow\"|" $HOME/.okp4d/config/app.toml

sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
  $HOME/.okp4d/config/app.toml
```

#### Custom Port Okp4

```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:44658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:44657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:44060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:44656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":44660\"%" $HOME/.okp4d/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:44317\"%; s%^address = \":8080\"%address = \":44080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:44090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:44091\"%; s%^address = \"0.0.0.0:8545\"%address = \"0.0.0.0:44545\"%; s%^ws-address = \"0.0.0.0:8546\"%ws-address = \"0.0.0.0:44546\"%" $HOME/.okp4d/config/app.toml
```

#### Disable Indexer (Optional)

```
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.okp4d/config/config.toml
```

#### Download Snapshot (update every 5 hour)

```
curl -L https://snap.nodexcapital.com/okp4/okp4-latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.okp4d
[[ -f $HOME/.okp4d/data/upgrade-info.json ]] && cp $HOME/.okp4d/data/upgrade-info.json $HOME/.okp4d/cosmovisor/genesis/upgrade-info.json
```

#### Start Service & Check Logs

```
sudo systemctl start okp4d && sudo journalctl -fu okp4d -o cat
```

