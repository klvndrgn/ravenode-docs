---
description: >-
  A Tendermint cheat sheet is a quick reference for essential commands to
  control Tendermint blockchains.
---

# 📝 Cheat Sheet

## Managing Keys

### Generate new key

```
bitbadgeschaind keys add wallet
```

### Recover key

```
bitbadgeschaind keys add wallet --recover
```

### List all key

```
bitbadgeschaind keys list
```

### Delete key

```
bitbadgeschaind keys delete wallet
```

### Export key

```
bitbadgeschaind keys export wallet
```

### Import key

```
bitbadgeschaind keys import wallet wallet.backup
```

### Query wallet balances

```
bitbadgeschaind q bank balances $(bitbadgeschaind keys show wallet -a)
```

## Managing validators

### Create Validator

```
bitbadgeschaind tx staking create-validator \
--amount 1000000000ubadge \
--pubkey $(bitbadgeschaind tendermint show-validator) \
--moniker "your-moniker-name" \
--identity "your-keybase-id" \
--details "your-details" \
--website "your-website" \
--security-contact "your-email" \
--chain-id bitbadges-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.5 \
--gas-prices 0.025ubadge \
--gas auto \
-y
```

### Edit validator

```
bitbadgeschaind tx staking edit-validator \
--new-moniker "your-moniker-name" \
--identity "your-keybase-id" \
--details "your-details" \
--website "your-website" \
--security-contact "your-email" \
--chain-id bitbadges-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.025ubadge \
-y
```

### Unjail validator

```
bitbadgeschaind tx slashing unjail --from wallet --chain-id bitbadges-1 --gas-adjustment 1.4 --gas auto --fees 0.025ubadge -y
```

### Validator unjail reason

```
bitbadgeschaind query slashing signing-info $(bitbadgeschaind tendermint show-validator)
```

### List active validator

```
bitbadgeschaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### List inactive validator

```
bitbadgeschaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### View validator details

```
bitbadgeschaind q staking validator $(bitbadgeschaind keys show wallet --bech val -a)
```

### Get Denom Info

```
bitbadgeschaind q bank denom-metadata -oj | jq
```

### Get Sync Status

```
bitbadgeschaind status 2>&1 | jq -r '.SyncInfo.catching_up // .sync_info.catching_up'
```

### Get Latest Height

```
bitbadgeschaind status 2>&1 | jq -r '.SyncInfo.latest_block_height // .sync_info.latest_block_height'
```

## Token Management

### Withdraw rewards from all validators

```
bitbadgeschaind tx distribution withdraw-all-rewards --from wallet --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025ubadge -y 
```

### Withdraw commission and rewards from your validator

```
bitbadgeschaind tx distribution withdraw-rewards $(bitbadgeschaind keys show wallet --bech val -a) --commission --from wallet --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025ubadge -y 
```

### Delegate tokens to your validator

```
bitbadgeschaind tx staking delegate $(bitbadgeschaind keys show wallet --bech val -a) 1000000000ubadge --from wallet --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025ubadge -y 
```

### Delegate token to other validator, change \<to-valoper-address> as you like

```
bitbadgeschaind tx staking delegate <to-valoper-address> 1000000000ubadge --from wallet --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025ubadge -y 
```

### Redelegate to another validator

```
bitbadgeschaind tx staking redelegate $(bitbadgeschaind keys show wallet --bech val -a) <to-valoper-address> 1000000000ubadge --from wallet --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025ubadge -y 
```

### Unbond token from your own validator

```
bitbadgeschaind tx staking unbond $(bitbadgeschaind keys show wallet --bech val -a) 1000000000ubadge --from wallet --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025ubadge -y
```

### Send token to the wallet, change \<to-wallet-address> as you like

```
bitbadgeschaind tx bank send wallet <to-wallet-address> 1000000000ubadge --from wallet --chain-id bitbadges-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025ubadge -y
```
