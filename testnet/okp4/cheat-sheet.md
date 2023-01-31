---
description: >-
  Useful set of commands for node operators. From key management to chain
  governance.
---

# 6 Cheat Sheet

### Key Management

```
okp4d keys add wallet #create new wallet
okp4d keys add wallet --recover #recover existing wallet
okp4d keys list #list all wallet
okp4d keys delete wallet #delete wallet
okp4d keys export wallet #export keys to file
okp4d keys import wallet wallet.backup #import keys from file
okp4d query bank balances $(planqd keys show wallet -a) #show walet balance
```

### Validator management

#### Create Validator

```
okp4d tx staking create-validator \
  --amount 1000000uknow \
  --from wallet \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1000000" \
  --pubkey $(okp4d tendermint show-validator) \
  --moniker $MONIKER \
  --chain-id okp4-nemeton-1  \
  --identity=""  \
  --website="" \
  --details="" \
  --gas=auto
  -y
```

#### Edit Validator

```
okp4d tx staking edit-validator \
--moniker="YOUR-MONIKER" \
--identity="Your Keybase ID" \
--details="Your Validator Details" \
--website="Your Website URL"
--chain-id=okp4-nemeton-1 \
--commission-rate=0.1 \
--from=wallet \
--gas=auto
-y
```

#### Unjail Validator

```
okp4d tx slashing unjail \
  --broadcast-mode=block \
  --from=wallet \
  --chain-id=okp4-nemeton-1 \
  --gas=auto
```

#### Remove Okp4 Nodes

{% hint style="warning" %}
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your **priv\_validator\_key.json**!
{% endhint %}

```
sudo systemctl stop okp4d && \
sudo systemctl disable okp4d && \
rm /etc/systemd/system/okp4d.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .okp4d && \
rm -rf $(which okp4d)
```
