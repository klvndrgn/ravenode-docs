# 🔄 Update

To make the upgrade process as flawless as possible, we will update the [ITN installer](https://github.com/dusk-network/itn-installer) script from time to time. This script can be ran in a non-destructive way, meaning it changes only what is needed.

To upgrade to the latest ITN version (v0.1.4), run:

```
curl --proto '=https' --tlsv1.2 -sSfL https://github.com/dusk-network/itn-installer/releases/download/v0.1.4/itn-installer.sh | sudo sh
```

Once it’s done and gives no errors, start Rusk again:

```
service rusk start
```

Check on the logs to see if it’s making and accepting new blocks:

```
tail -F /var/log/rusk.log
```

{% hint style="warning" %}
If you see a **NETWORK MISMATCH** please note that it is **NOT** an error. It simply means that your node successfully joined the new network ID and is rejecting messages from nodes that did not upgrade
{% endhint %}
