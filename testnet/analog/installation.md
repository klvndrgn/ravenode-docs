---
description: Guide on Timechain Node for Analog by Ravenode
---

# 🖥️ Installation

{% hint style="info" %}
Currently, registering to become a Time Node operator requires permission. To join, please fill out this [form](https://l5d87lam6fy.typeform.com/to/kwlADm6U) to be added to the waitlist.
{% endhint %}

## **Hardware Specifications**

<table><thead><tr><th width="186">CPU</th><th width="89">RAM</th><th width="141">Storage</th><th width="154">Network Port</th><th>Bandwidth</th></tr></thead><tbody><tr><td>8 vCPUs (8 Cores)</td><td>16 GB</td><td>300 GB NVMe SSD</td><td>9944, 30303 TCP</td><td>500 MBps for Download / Upload</td></tr></tbody></table>

## Update System <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt update && sudo apt upgrade -y
```

## Install Prerequisites & Docker

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```

## Verify Docker Installation <a href="#download-binary--copy-to-usrbin" id="download-binary--copy-to-usrbin"></a>

```
docker --version
```

{% hint style="info" %}
You should see output like `Docker version 26.1.4, build 5650f9b`
{% endhint %}

## Download Timechain Docker Image

```
docker pull analoglabs/timechain
```

## Create Analog Directory

```
mkdir -p $(pwd)/.analog
```

## Run Timechain Node

```
docker run -d --name analog -p 9944:9944 -p 30303:30303 -v $(pwd)/.analog:/.analog analoglabs/timechain \
    --base-path /.analog \
    --rpc-external \
    --unsafe-rpc-external \
    --rpc-cors all \
    --name YourMoniker \
    --telemetry-url="wss://telemetry.analog.one/submit 9" \
    --rpc-methods Unsafe
```

{% hint style="danger" %}
Change **YourMoniker** to your node name
{% endhint %}

## Check Node Logs

```
docker logs -f analog
```

* Example Output

```
2024-06-23 15:36:54 ✨ Imported #1031546 (0x98b1…6fda)    
2024-06-23 15:36:57 Report 12D3KooWPADeY26Cr3TKr6XFndCwN8M2g7EkYw7HWoFFbNweWbob: -2147483648 to -2147483648. Reason: Genesis mismatch. Banned, disconnecting.    
2024-06-23 15:36:58 💤 Idle (2 peers), best: #1031546 (0x98b1…6fda), finalized #1031544 (0x9cd8…4133), ⬇ 2.8kiB/s ⬆ 1.9kiB/s    
2024-06-23 15:37:00 ✨ Imported #1031547 (0x1b00…d990)    
2024-06-23 15:37:01 ✨ Imported #1031547 (0x8f1b…2298)    
2024-06-23 15:37:03 💤 Idle (2 peers), best: #1031547 (0x1b00…d990), finalized #1031545 (0xf1ee…482c), ⬇ 3.1kiB/s ⬆ 1.7kiB/s    
2024-06-23 15:37:06 ✨ Imported #1031548 (0xee15…5000)    
2024-06-23 15:37:08 💤 Idle (2 peers), best: #1031548 (0xee15…5000), finalized #1031546 (0x98b1…6fda), ⬇ 2.2kiB/s ⬆ 2.0kiB/s    
2024-06-23 15:37:12 ✨ Imported #1031549 (0xdb13…a2f7)    
2024-06-23 15:37:13 💤 Idle (2 peers), best: #1031549 (0xdb13…a2f7), finalized #1031546 (0x98b1…6fda), ⬇ 6.1kiB/s ⬆ 2.3kiB/s    
```

## Install Websocat

```
sudo wget -qO /usr/local/bin/websocat https://github.com/vi/websocat/releases/latest/download/websocat.x86_64-unknown-linux-musl
```

## Verify Websocat Instalation

```
sudo chmod a+x /usr/local/bin/websocat && websocat --version
```

{% hint style="info" %}
You should see output like `websocat 1.13.0`
{% endhint %}

## Bond TANLOG And Setup Validator Account <a href="#bond-tnt-and-setup-validator-account" id="bond-tnt-and-setup-validator-account"></a>

* Go to [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Frpc.testnet.analog.one#/staking/actions)
* Create new stash account
* Choose **Stash** account, then fill the amount to bond (Minimum **0.9 TANLOG)**

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Validators must have a minimum stash in their accounts (0.9 TANLOG)
{% endhint %}

## Generate Session Key

```
echo '{"id":1,"jsonrpc":"2.0","method":"author_rotateKeys","params":[]}' | websocat -n1 -B 99999999 ws://127.0.0.1:9944
```

Example Output:

```
{"jsonrpc":"2.0","result":"0xb4975e1b541......77d86128917540caf1d","id":1}
```

* Copy the `0x` Output
* Back to [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Frpc.testnet.analog.one#/staking/actions)
* Click **Set Session Key**
* Input the `0x` Output before
* Validator created successfully
* Check if your validator is on the [telemetry](https://telemetry.analog.one/#/0x0614f7b74a2e47f7c8d8e2a5335be84bdde9402a43f5decdec03200a87c8b943)

{% hint style="warning" %}
Backup Session Key save it to your text editor app for later use
{% endhint %}

<div data-full-width="true"><figure><img src="../../.gitbook/assets/HCA logo.jpg" alt="" width="160"><figcaption><p>Happy Cuan Airdrop</p></figcaption></figure></div>

{% embed url="https://t.me/HappyCuanAirdrop" %}
Join our Telegram group for the latest updates and discussions!
{% endembed %}
