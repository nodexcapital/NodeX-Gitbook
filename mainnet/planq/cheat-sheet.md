---
description: >-
  Useful set of commands for node operators. From key management to chain
  governance.
---

# 6 Cheat Sheet

### Key Management

```
planqd keys add wallet #create new wallet
planqd keys add wallet --recover #recover existing wallet
planqd  keys list #list all wallet
planqd keys delete wallet #delete wallet
planqd keys export wallet #export keys to file
planqd keys import wallet wallet.backup #import keys from file
planqd q bank balances $(planqd keys show wallet -a) #show walet balance
```

### Validator management

#### Create Validator

```
planqd tx staking create-validator \
  --amount 10000000000000000000aplanq \
  --from wallet \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1000000" \
  --pubkey $(planqd tendermint show-validator) \
  --moniker $MONIKER \
  --chain-id planq_7070-2 \
  --identity=""  \
  --website="" \
  --details="" \
  --gas="1000000" \
  --gas-prices="30000000000aplanq" \
  --gas-adjustment="1.15" \
  -y
```

#### Edit Validator

```
planqd tx staking edit-validator \
--moniker="YOUR-MONIKER" \
--identity="Your Keybase ID" \
--details="Your Validator Details" \
--website="Your Website URL"
--chain-id=planq_7070-2 \
--commission-rate=0.1 \
--from=wallet \
--gas="1000000" \
--gas-prices="30000000000aplanq" \
--gas-adjustment="1.15" \
-y
```

#### Unjail Validator

```
planqd tx slashing unjail \
  --broadcast-mode=block \
  --from=wallet \
  --chain-id=planq_7070-2 \
  --gas=auto
```

#### Remove Planq Nodes

{% hint style="warning" %}
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!
{% endhint %}

```
sudo systemctl stop planqd && \
sudo systemctl disable planqd && \
rm /etc/systemd/system/planqd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .planqd && \
rm -rf $(which planqd)
```
