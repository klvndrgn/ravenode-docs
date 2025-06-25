---
description: >-
  A Tendermint cheat sheet is a quick reference for essential commands to
  control Tendermint blockchains.
---

# 📝 Cheat Sheet

## Managing Keys

### Generate new key

```
symphonyd keys add wallet
```

### Recover key

```
symphonyd keys add wallet --recover
```

### List all key

```
symphonyd keys list
```

### Delete key

```
symphonyd keys delete wallet
```

### Export key

```
symphonyd keys export wallet
```

### Import key

```
symphonyd keys import wallet wallet.backup
```

### Query wallet balances

```
symphonyd q bank balances $(symphonyd keys show wallet -a)
```

## Managing validators

### Create Validator

```
symphonyd tx staking create-validator \
--amount 1000000note \
--pubkey $(symphonyd tendermint show-validator) \
--moniker "your-moniker-name" \
--identity "your-keybase-id" \
--details "your-details" \
--website "your-website" \
--security-contact "your-email" \
--chain-id symphony-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.5 \
--gas-prices 0.0025note \
--gas auto \
-y
```

### Edit validator

```
symphonyd tx staking edit-validator \
--new-moniker "your-moniker-name" \
--identity "your-keybase-id" \
--details "your-details" \
--website "your-website" \
--security-contact "your-email" \
--chain-id symphony-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.0025note \
-y
```

### Unjail validator

```
symphonyd tx slashing unjail --from wallet --chain-id symphony-testnet-2 --gas-adjustment 1.4 --gas auto --fees 800note -y
```

### Validator unjail reason

```
symphonyd query slashing signing-info $(symphonyd tendermint show-validator)
```

### List active validator

```
symphonyd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### List inactive validator

```
symphonyd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```

### View validator details

```
symphonyd q staking validator $(symphonyd keys show wallet --bech val -a)
```

### Get Denom Info

```
symphonyd q bank denom-metadata -oj | jq
```

### Get Sync Status

```
symphonyd status 2>&1 | jq -r '.SyncInfo.catching_up // .sync_info.catching_up'
```

### Get Latest Height

```
symphonyd status 2>&1 | jq -r '.SyncInfo.latest_block_height // .sync_info.latest_block_height'
```

## Token Management

### Withdraw rewards from all validators

```
symphonyd tx distribution withdraw-all-rewards --from wallet --chain-id symphony-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.0025note -y 
```

### Withdraw commission and rewards from your validator

```
symphonyd tx distribution withdraw-rewards $(symphonyd keys show wallet --bech val -a) --commission --from wallet --chain-id symphony-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.0025note -y 
```

### Delegate tokens to your validator

```
symphonyd tx staking delegate $(symphonyd keys show wallet --bech val -a) 1000000note --from wallet --chain-id symphony-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.0025note -y 
```

### Delegate token to other validator, change \<to-valoper-address> as you like

```
symphonyd tx staking delegate <to-valoper-address> 1000000note --from wallet --chain-id symphony-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.0025note -y 
```

### Redelegate to another validator

```
symphonyd tx staking redelegate $(symphonyd keys show wallet --bech val -a) <to-valoper-address> 1000000note --from wallet --chain-id symphony-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.0025note -y 
```

### Unbond token from your own validator

```
symphonyd tx staking unbond $(symphonyd keys show wallet --bech val -a) 1000000note --from wallet --chain-id symphony-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.0025note -y
```

### Send token to the wallet, change \<to-wallet-address> as you like

```
symphonyd tx bank send wallet <to-wallet-address> 1000000note --from wallet --chain-id symphony-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.0025note -y
```
