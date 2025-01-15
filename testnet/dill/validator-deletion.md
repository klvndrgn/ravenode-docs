---
description: Proceed carefully when initiating this deletion process
---

# ⚠️ Validator Deletion

## Stop the Node

```
pkill dill-node
```

## Remove node files and directories

```
rm -rf ~/dill
```

## Remove logs and config files (Optional)

```
sudo rm -rf /var/log/dill
sudo rm -rf /etc/dill
```
