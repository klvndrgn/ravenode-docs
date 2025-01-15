---
description: >-
  Set up a verifier to run in the background, ensuring that data shares remain
  intact and helping maintain the network’s integrity
---

# 🖥️ Installation

{% hint style="info" %}
Go to the [faucet](https://faucet.testnet.nillion.com/) to obtain NIL in your Keplr wallet
{% endhint %}

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
You should see output like `Docker version 27.1.2, build d01f264`
{% endhint %}

## Download accuser Image

```
docker pull nillion/retailtoken-accuser:v1.0.0
```

## Create accuser directory

```
mkdir -p nillion/accuser
```

## Run the container to initialise accuser & register

```
docker run -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.0 initialise
```

This will output the details needed to register the accuser on the [website](https://verifier.nillion.com/verifier), connect your keplr wallet & register them below:

* accound\_id: Nillion address of the accuser
* public\_key: Public Key of the accuser

{% hint style="danger" %}
The accuser will save the credentials in a file named credentials.json within the created folder. It's important to back up this file if you lose it, you'll lose access to the keys and address of the accuser.
{% endhint %}

## Funding the accuser

To file accusations on Nilchain, you must first fund the accuser account with NIL. You can obtain this from the [Nillion faucet](https://faucet.testnet.nillion.com/)

{% hint style="info" %}
The Nillion address of the accuser is the one that was generated and entered in the previous step (not your Keplr wallet address).
{% endhint %}

## Running the accuser

{% hint style="warning" %}
YOU MUST WAIT 30-60 MINUTES TO CONTINUE WITH THE STEPS BELOW
{% endhint %}

<figure><img src="../../.gitbook/assets/2024-08-28 09_06_02-Address nillion1he...9pfn Details - Nillion (NIL) Blockchain Explorer.png" alt=""><figcaption></figcaption></figure>

```
docker run -d -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.0 accuse --rpc-endpoint "https://testnet-nillion-rpc.lavenderfive.com/" --block-start 5096617
```

{% hint style="info" %}
Adjust the `--block-start` value to the block height just before your registered verifier. To find this height, check the address you used to register your verifier on your connected Keplr wallet, then look up the transaction hash of type **"Pay For"** on the blockchain explorer to determine the block height where your verifier was registered
{% endhint %}

## Check verifier logs

```
# List Available Docker Container
docker ps

# Display logs
docker logs -f <container-id>
```

<figure><img src="../../.gitbook/assets/photo_2024-08-28_09-32-30.jpg" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Wait until it synchronizes with the current block height. Once it does, you should see if the status shows "Registered" as true.
{% endhint %}

<div data-full-width="true"><figure><img src="../../.gitbook/assets/HCA logo.jpg" alt="" width="160"><figcaption><p>Happy Cuan Airdrop</p></figcaption></figure></div>

{% embed url="https://t.me/HappyCuanAirdrop" %}
Join our Telegram group for the latest updates and discussions!
{% endembed %}
