---
description: Proceed carefully when initiating this deletion process
---

# ⚠️ Node Deletion

## Service Management

### Reload Services

```
sudo systemctl daemon-reload
```

### Enable Service

```
sudo systemctl enable bitbadgeschaind
```

### Disable Service

```
sudo systemctl disable bitbadgeschaind
```

### Run Service

```
sudo systemctl start bitbadgeschaind
```

### Stop Service

```
sudo systemctl stop bitbadgeschaind
```

### Restart Service

<pre><code><strong>sudo systemctl restart bitbadgeschaind
</strong></code></pre>

### Check Service Status

```
sudo systemctl status bitbadgeschaind
```

### Check Service Logs

```
sudo journalctl -u bitbadgeschaind -f --no-hostname -o cat
```

### Remove Node

```
cd $HOME && sudo systemctl stop bitbadgeschaind && sudo systemctl disable bitbadgeschaind && sudo rm /etc/systemd/system/bitbadgeschaind.service && sudo systemctl daemon-reload && sudo rm -rf $(which bitbadgeschaind) && sudo rm -rf $HOME/.bitbadgeschain && sudo rm -rf $(which bitbadgeschaind)
```
