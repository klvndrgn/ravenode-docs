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
--amount 10000note \
--pubkey $(symphonyd tendermint show-validator) \
--moniker "your-moniker-name" \
--identity "your-keybase-id" \
--details "your-details" \
--website "your-website" \
--security-contact "your-email" \
--chain-id symphony-testnet-2 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--fees 800note \
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
--chain-id symphony-testnet-2 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--fees 800note \
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

