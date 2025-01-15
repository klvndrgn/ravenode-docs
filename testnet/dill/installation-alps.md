---
description: Guide on Light/Full Validator for Dill by Ravenode
---

# 🖥️ Installation (Alps)

{% hint style="info" %}
Currently, to become a Dill Light Validator you need the _**alps-validator**_ role on the Dill discord server.
{% endhint %}

## **Hardware Specifications**

## Light Validator

<table><thead><tr><th width="186">CPU</th><th width="89">RAM</th><th width="141">Storage</th><th width="171">Operating System</th><th>Bandwidth</th></tr></thead><tbody><tr><td>2 Cores</td><td>2 GB</td><td>20 GB</td><td>Ubuntu 22.04.2</td><td>8 Mb/S</td></tr></tbody></table>

## Full Validator

<table><thead><tr><th width="186">CPU</th><th width="89">RAM</th><th width="141">Storage</th><th width="174">Operating System</th><th>Bandwidth</th></tr></thead><tbody><tr><td>4 Cores</td><td>8 GB</td><td>256 GB</td><td>Ubuntu 22.04.2</td><td>64 Mb/S</td></tr></tbody></table>

## Add Alps Testnet Network

<table><thead><tr><th width="299">Custom Network</th><th>Details</th></tr></thead><tbody><tr><td>Network Name</td><td>Alps Testnet</td></tr><tr><td>RPC URL</td><td><a href="https://rpc-alps.dill.xyz/">https://rpc-alps.dill.xyz/</a></td></tr><tr><td>Chain ID</td><td>102125</td></tr><tr><td>Token Symbol:</td><td>DILL</td></tr><tr><td>Block explorer URL</td><td><a href="https://alps.dill.xyz/">https://alps.dill.xyz/</a></td></tr></tbody></table>

## Request Faucet

1. Requires _**alps-validator**_ Role
2. Go to private \[🌋｜alps] channel on Dill discord server
3. Copy your EVM (0x) address
4. Type **$request address**

If you have more than 1 quota use this to claim all in 1 wallet address, adjust **quota\_amount** to your unused quota:

Type **$request address quota\_amount**

Type **$request history** to check your faucet request history

{% hint style="warning" %}
Please double check your address as one user can only request on time
{% endhint %}

## Update System <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt update && sudo apt upgrade -y
```

## **Download and run the setup script**

```
curl -sO https://raw.githubusercontent.com/DillLabs/launch-dill-node/main/dill.sh  && chmod +x dill.sh && ./dill.sh
```

## Running Validator

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p>This is the first step when running the setup script</p></figcaption></figure>

```
1. Launch a new dill node
2. Add validator to existing node
```

1. Select option 1 to launch your first new Dill Alps node.
2. Select option 2 if you have more than one quota to run a node.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption><p>Select options to generate Validator keys</p></figcaption></figure>

1. Select option 1 if you have more than one quota to run a node, allowing you to add an additional validator to your node.
2. Select option 2 to use the old Andes validator mnemonic and import it using the mnemonic.

_\*In my case for my old andes validator i choose option 2, then I also have 3 quota to run other light validator which means i need to select option 1 to generate new validator_

{% hint style="warning" %}
**Option 1,** Please backup the **validator\_keys** folder on this directory /root/dill/validator\_keys
{% endhint %}

{% hint style="warning" %}
**Option 2, m**ake sure you have backup the **validator\_keys** from old Andes Validator, since this is needed to import the old Andes validator to run on the new Alps Validator. Select option 2 and paste your seed phrase when prompted and hit enter.
{% endhint %}

* Wait for the files to download.
* Save the generated Keystore Password.
* Press enter to confirm after validator keys generated.

If you completed all the steps correctly, you will get an output like this:

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## Check your node health

<pre><code><strong>cd dill
</strong>./health_check.sh -v
</code></pre>

If you receive an output like this, it means your node is synced:

```
2024-09-15 10:53:34 +08 > node running
2024-09-15 10:53:34 +08 > node peers count 89
2024-09-15 10:53:34 +08 > node's network connectivity check passed
2024-09-15 10:53:34 +08 > Synchronization done, current slot is 55092
2024-09-15 10:53:34 +08 > Node health check passed.
```

## Add new validator to the node (if multiple validators existed)

```
cd dill
./2_add_validator.sh
```

Since this is a new validator, I prefer to keep the mnemonic separate. Choose option 1 when prompted to generate the validator keys.

The option for deposit token amount entered here needs to be the same as the one chosen when adding the same validator on the original node

After that get the new validator **deposit\_data-xxxx.json** and complete the validator staking process

## Validator Staking <a href="#download-binary--copy-to-usrbin" id="download-binary--copy-to-usrbin"></a>

Please ensure your address have at least 3600 DILL for Light Validator & 36000 DILL for Full Validator

## Upload Deposit Data

1. Visit [https://staking.dill.xyz/](https://staking.dill.xyz/)
2. Upload the **deposit\_data-xxxx.json** file

<figure><img src="../../.gitbook/assets/dill 1.png" alt=""><figcaption><p>Drag file to upload or click + icon to browse from your file explorer</p></figcaption></figure>

3. After uploading the **deposit\_data-xxxx.json** file to the site, click Connect to MetaMask, make sure you have enough funds

<figure><img src="../../.gitbook/assets/2024-09-15 09_56_18-Media Player.png" alt=""><figcaption></figcaption></figure>

4. Send deposit, using MetaMask to send a deposit transaction

<figure><img src="../../.gitbook/assets/DILL 3.png" alt=""><figcaption></figcaption></figure>

5. After completing these operations, you can check your node using your public key (prefixed with 0x) on the last pages of the validators section in the Explorer at [https://alps.dill.xyz/validators](https://alps.dill.xyz/validators). Please note that it may take up to an hour for your node to appear.

<div data-full-width="true"><figure><img src="../../.gitbook/assets/HCA logo.jpg" alt="" width="160"><figcaption><p>Happy Cuan Airdrop</p></figcaption></figure></div>

{% embed url="https://t.me/HappyCuanAirdrop" %}
Join our Telegram group for the latest updates and discussions!
{% endembed %}
