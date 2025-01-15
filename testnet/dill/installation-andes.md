---
description: Guide on Light Validator for Dill by Ravenode
---

# 🖥️ Installation (Andes)

{% hint style="info" %}
Currently, to become a Dill Light Validator you need the _**andes-validator**_ role on the Dill discord server.
{% endhint %}

## **Hardware Specifications**

<table><thead><tr><th width="186">CPU</th><th width="89">RAM</th><th width="141">Storage</th><th width="154">Operating System</th><th>Bandwidth</th></tr></thead><tbody><tr><td>2 Cores</td><td>2 GB</td><td>20 GB</td><td>Ubuntu 22.04.2</td><td>1 MB/S</td></tr></tbody></table>

## Add Andes Testnet Network

<table><thead><tr><th width="299">Custom Network</th><th>Details</th></tr></thead><tbody><tr><td>Network Name</td><td>Dill Testnet Andes</td></tr><tr><td>RPC URL</td><td><a href="https://rpc-andes.dill.xyz/">https://rpc-andes.dill.xyz/</a></td></tr><tr><td>Chain ID</td><td>558329</td></tr><tr><td>Token Symbol:</td><td>DILL</td></tr><tr><td>Block explorer URL</td><td><a href="https://andes.dill.xyz/">https://andes.dill.xyz/</a></td></tr></tbody></table>

## Request Faucet

1. Requires _**andes-validator**_ Role
2. Go to private \[🌋｜andes] channel on Dill discord server
3. Copy your EVM (0x) address
4. Type **$request address**

{% hint style="warning" %}
Please double check your address as one user can only request on time
{% endhint %}

## Update System <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt update && sudo apt upgrade -y
```

## **Download and run the setup script**

```
curl -O https://raw.githubusercontent.com/DillLabs/launch-dill-node/main/launch_dill_node.sh  && chmod +x launch_dill_node.sh && ./launch_dill_node.sh
```

* Wait for the files to download.
* Create a strong password for your keystore (At least 8 characters long) and confirm Keystore Password.
* Press enter to confirm after validator keys generated.

If you completed all the steps correctly, you will get an output like this:

```
node running, congratulations 😄
validator pubkey: xxxxxx
Please backup this directory $YOUR_SCRIPT_PATH/dill/validator_keys. Required for recovery and migration. Important ！！！
```

{% hint style="warning" %}
Please backup the **validator\_keys** folder on this directory /root/dill/validator\_keys
{% endhint %}

## Check your node health

<pre><code><strong>cd dill
</strong>./health_check.sh -v
</code></pre>

If you receive an output like this, it means your node is synced:

```
2024-07-26 15:13:13 +08 > node running
2024-07-26 15:13:13 +08 > node peers count 81
2024-07-26 15:13:13 +08 > node's network connectivity check passed
2024-07-26 15:13:13 +08 > Synchronization done, current slot is 121826
2024-07-26 15:13:13 +08 > Node health check passed.
```

## Validator Staking <a href="#download-binary--copy-to-usrbin" id="download-binary--copy-to-usrbin"></a>

Please ensure your address have at least >2500 DILL requested from the faucet channel.

## Upload Deposit Data

1. Visit [https://staking.dill.xyz/](https://staking.dill.xyz/)
2. Upload the **deposit\_data-xxxx.json** file

<figure><img src="../../.gitbook/assets/dill 1.png" alt=""><figcaption><p>Drag file to upload or click + icon to browse from your file explorer</p></figcaption></figure>

3. After uploading the **deposit\_data-xxxx.json** file to the site, click Connect to MetaMask, make sure you have enough funds (>2500 DILL)

<figure><img src="../../.gitbook/assets/dill 2.png" alt=""><figcaption><p>Click Continue</p></figcaption></figure>

4. Send deposit, using MetaMask to send a deposit transaction

<figure><img src="../../.gitbook/assets/DILL 3.png" alt=""><figcaption></figcaption></figure>

5. After completing these operations, you can check your node using your public key (prefixed with 0x) on the last pages of the validators section in the Explorer at [https://andes.dill.xyz/validators](https://andes.dill.xyz/validators). Please note that it may take up to an hour for your node to appear.

<div data-full-width="true"><figure><img src="../../.gitbook/assets/HCA logo.jpg" alt="" width="160"><figcaption><p>Happy Cuan Airdrop</p></figcaption></figure></div>

{% embed url="https://t.me/HappyCuanAirdrop" %}
Join our Telegram group for the latest updates and discussions!
{% endembed %}
