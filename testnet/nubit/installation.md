# 🖥️ Installation

## **Hardware Specifications**

<table><thead><tr><th width="202">CPU</th><th width="156">RAM</th><th>Storage</th><th>Bandwidth</th></tr></thead><tbody><tr><td>Single Core</td><td>500 MB</td><td>100 GB SSD</td><td>0.1 Mbps Upload/Downlaod</td></tr></tbody></table>

## Run Light Node <a href="#install-dependencies" id="install-dependencies"></a>

```
curl -sL1 https://nubit.sh | bash
```

{% hint style="warning" %}
Copy & backup the Pubkey to verify on [https://alpha.nubit.org/](https://alpha.nubit.org/)
{% endhint %}

## Run Using Daemon Service

Create the service by running this command below:

```
sudo tee /etc/systemd/system/nubit.service > /dev/null <<EOF                                                              
[Unit]
Description=Nubit Light Node
After=network.target

[Service]
User=root
WorkingDirectory=/root/nubit-node
ExecStart=/root/nubit-node/start.sh
Restart=always
RestartSec=10
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=nubit-node

[Install]
WantedBy=multi-user.target
EOF
```

## Enable & Start Service <a href="#download-binary--copy-to-usrbin" id="download-binary--copy-to-usrbin"></a>

```
sudo systemctl daemon-reload
sudo systemctl enable nubit
sudo systemctl start nubit
```

## Check Node Logs

```
sudo journalctl -u nubit -f --no-hostname -o cat
```

## Run Using Screen

Install the Screen package by running this command below:

```
sudo apt-get install screen
```

## Verify Screen Installation

```
screen --version
```

## Create Screen

```
screen -S nubit
```

## Run Light Node

```
curl -sL1 https://nubit.sh | bash
```

{% hint style="warning" %}
Copy & backup the Pubkey to verify on [https://alpha.nubit.org/](https://alpha.nubit.org/)
{% endhint %}

If you want to exit a screen session, simply press `Ctrl + A` followed by `D`.

If  you want to reattach to a session, use this command below

```
screen -r nubit
```

## Backup Generated Wallet Seed Phrase

```
cd nubit-node
sudo cat mnemonic.txt
```

{% hint style="warning" %}
Copy all 24 words and save them to your backup somewhere.
{% endhint %}

## Import Existing Wallet to Node

Official guide: [https://docs.nubit.org/nubit-da/interact-with-nubit-da/manage-keys](https://docs.nubit.org/nubit-da/interact-with-nubit-da/manage-keys)

### Set Environment

Navigate to the nubit-node directory

```
cd nubit-node
```

Set the path and environment variables. Set `NODE_TYPE` to full if you are running a full node. In this case, we are running a light client

```
export PATH=$PATH:$(pwd)/bin
NETWORK=nubit-alphatestnet-1
NODE_TYPE=light
store=$HOME/.nubit-${NODE_TYPE}-${NETWORK}/
```

### List Keys

Use the following command to view your Nubit address list:

```
nkey list --p2p.network $NETWORK --node.type $NODE_TYPE
```

### Import Using M**nemonic Phrase** Into Nubit-Node

```
nkey add my_nubit_key --recover --keyring-backend test --node.type $NODE_TYPE --p2p.network $NETWORK
```

Enter your mnemonic phrase, when prompted with `"Enter your bip39 mnemonic,"`

### Import Using Private Key Into Nubit-Node

Nubit-node also supports importing addresses using private key files. Refer to the following command, replacing `my_nubit_key` with the desired key name and `~/nubit-da/nubit-node/account1.private` with the actual location of your private key file:

```
nkey import my_nubit_key ~/nubit-da/nubit-node/account1.private --keyring-backend test --node.type $NODE_TYPE --p2p.network $NETWORK
```

{% hint style="danger" %}
If you want to delete all your keys, run this command below:
{% endhint %}

```
rm -rf $HOME/.nubit-$NODE_TYPE*
```

{% hint style="danger" %}
If you want to delete specific keys, run this command below:
{% endhint %}

```
nkey delete (YOUR_KEY_NAME) --keyring-backend test --p2p.network $NETWORK --node.type $NODE_TYPE
```
