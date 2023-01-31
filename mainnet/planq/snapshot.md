---
description: Catch the latest block faster by using our daily snapshots.
---

# 3 Snapshot

<figure><img src="../../.gitbook/assets/planq.png" alt=""><figcaption></figcaption></figure>

**Network:** Mainnet **| Chain ID:** planq\_7070-2 | **Node Version:** v1.0.3

{% hint style="info" %}
Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.
{% endhint %}

Snapshot taken automatically every 5 hours starting at **22:00 UTC**

| Pruning Custom | Node Version | Download                                                                         |
| -------------- | ------------ | -------------------------------------------------------------------------------- |
| 100/0/10       | v1.0.3       | [planq-latest.tar.lz4](https://snap.nodexcapital.com/planq/planq-latest.tar.lz4) |

### Instructions

#### Stop the service and reset the data

```
sudo systemctl stop planqd
cp $HOME/.planqd/data/priv_validator_state.json $HOME/.planqd/priv_validator_state.json.backup
rm -rf $HOME/.planqd/data
```

#### Download latest snapshot

```
curl -L https://snap.nodexcapital.com/planq/planq-latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.planqd
mv $HOME/.planqd/priv_validator_state.json.backup $HOME/.planqd/data/priv_validator_state.json
```

#### Restart Chain & Check Logs

```
sudo systemctl start planqd && sudo journalctl -fu planqd -o cat
```

