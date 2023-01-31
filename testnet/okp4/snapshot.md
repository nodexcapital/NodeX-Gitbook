---
description: Catch the latest block faster by using our daily snapshots.
---

# 3 Snapshot

<figure><img src="https://raw.githubusercontent.com/kj89/testnet_manuals/main/pingpub/logos/okp4.png" alt=""><figcaption></figcaption></figure>

**Network:** Testnet **| Chain ID:** okp4-nemeton-1 | **Version:** v3.0.0

{% hint style="info" %}
Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.
{% endhint %}

Snapshot taken automatically every 5 hours starting at **22:00 UTC**

| Pruning Custom | Node Version | Download                                                                      |
| -------------- | ------------ | ----------------------------------------------------------------------------- |
| 100/0/10       | v3.0.0       | [okp4-latest.tar.lz4](https://snap.nodexcapital.com/okp4/okp4-latest.tar.lz4) |

### Instructions

#### Stop the service and reset the data

```
sudo systemctl stop okp4d
cp $HOME/.okp4d/data/priv_validator_state.json $HOME/.okp4d/priv_validator_state.json.backup
rm -rf $HOME/.okp4d/data
```

#### Download latest snapshot

```
curl -L https://snap.nodexcapital.com/okp4/okp4-latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.okp4d
mv $HOME/.okp4d/priv_validator_state.json.backup $HOME/.okp4d/data/priv_validator_state.json
```

#### Restart Chain & Check Logs

```
sudo systemctl start okp4d && sudo journalctl -fu okp4d -o cat
```

