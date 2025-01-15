---
description: Proceed carefully when initiating this deletion process
---

# ⚠️ Node Deletion

## Remove node

```
cd $HOME
sudo systemctl stop symphony
sudo systemctl disable symphony
sudo rm /etc/systemd/system/symphony.service
sudo systemctl daemon-reload
sudo rm -f $(which symphonyd)
sudo rm -rf $HOME/.symphonyd
sudo rm -rf $HOME/symphony
sudo rm -rf $HOME/go
```
