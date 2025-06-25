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
sudo systemctl enable symphonyd
```

### Disable Service

```
sudo systemctl disable symphonyd
```

### Run Service

```
sudo systemctl start symphonyd
```

### Stop Service

```
sudo systemctl stop symphonyd
```

### Restart Service

<pre><code><strong>sudo systemctl restart symphonyd
</strong></code></pre>

### Check Service Status

```
sudo systemctl status symphonyd
```

### Check Service Logs

```
sudo journalctl -u symphonyd -f --no-hostname -o cat
```

### Remove Node

```
cd $HOME && sudo systemctl stop symphonyd && sudo systemctl disable symphonyd && sudo rm /etc/systemd/system/symphonyd.service && sudo systemctl daemon-reload && sudo rm -rf $(which symphonyd) && sudo rm -rf $HOME/.symphonyd && sudo rm -rf $(which symphonyd)
```
