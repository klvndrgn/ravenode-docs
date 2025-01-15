---
description: >-
  DISCLAIMER: THIS I CAN RUN USING CONTABO VPS, YOU NEED TO PAY ATTENTION TO THE
  PATH NAMING SECTION OF YOUR RESPECTIVE HOME FOLDERS
---

# 🖥️ Installation

More details about the incentive program :&#x20;

<mark style="color:blue;">https://www.stratisplatform.com/2024/02/07/500k-strax-airdrop-staking-quick-start-guide/</mark>&#x20;

Okay let’s continue running the stratis validator node.

Port Requirements&#x20;

GETH: Allow: 30303/UDP+TCP in+out; Block: 8545/TCP all

Prysm beacon-chain: Allow: \*/TCP+UDP out, 13000/TCP in+out, 12000/UDP in+out; Block: 3500/TCP all, 8551/TCP all, 4000/TCP all

Validator Requirements 20,000 STRAX Testnet

Install dependencies

```
sudo apt update && sudo apt upgrade -y &&\
sudo apt install git build-essential curl jq wget tar ufw -y
```

Port configure ( It’s default but you can costum with flags)

```
sudo ufw allow ssh
sudo ufw allow 30303/tcp
sudo ufw allow 13000
sudo ufw allow 12000
sudo ufw enable
sudo ufw reload

```

Install Go

```
wget "https://go.dev/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"

echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
```

check go

```
go version
```

Create \~/go/bin directory

```
cd $HOME
mkdir -p go/bin
```

Configure and Run an Execution Client ( geth )

```
get Geth
```

```
cd $HOME &&\
wget https://github.com/stratisproject/go-stratis/releases/download/0.1.1/geth-linux-amd64-5c4504c.tar.gz &&\
tar -xzf geth-linux-amd64-5c4504c.tar.gz &&\
rm -r geth-linux-amd64-5c4504c.tar.gz &&\
mv ./geth ./go/bin
```

<pre><code>sudo tee /etc/systemd/system/geth.service > /dev/null &#x3C;&#x3C;EOF
[Unit]
Description="geth client"
After=network-online.target

[Service]
User=$USER
ExecStart=$(which geth) --auroria --http --http.api eth,net,engine,admin --datadir=$HOME/.geth --authrpc.addr=127.0.0.1 --authrpc.jwtsecret=jwtsecret --syncmode=full
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF


<strong># Start geth client
</strong>sudo systemctl daemon-reload
sudo systemctl enable geth
sudo systemctl restart geth
journalctl -fu geth -o cat

</code></pre>

You should see logs like this, meaning geth is running and synchronizing to the highest block.

Configure a Beacon Client

```
cd $HOME
wget https://github.com/stratisproject/prysm-stratis/releases/download/0.1.1/beacon-chain-linux-amd64-0ebd251.tar.gz
tar -xzf beacon-chain-linux-amd64-0ebd251.tar.gz
rm -r beacon-chain-linux-amd64-0ebd251.tar.gz
mv ./beacon-chain ~/go/bin

# Create Beacon Service
tee /etc/systemd/system/beacon-chain.service > /dev/null <<EOF
[Unit]
Description="Beacon client"
After=network-online.target

[Service]
User=$USER
ExecStart=$(which beacon-chain) --auroria --datadir=$HOME/.beacon-chain --execution-endpoint=http://localhost:8551 --jwt-secret=jwtsecret --accept-terms-of-use
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

#Run Beacon
sudo systemctl daemon-reload
sudo systemctl enable beacon-chain
sudo systemctl restart beacon-chain
journalctl -fu beacon-chain -o cat

```

Generate Keys and Import

Go to <mark style="color:blue;">https://auroria.launchpad.stratisevm.com/en/overview</mark> Please confirm until “Generate key pairs” appears, And input your Metamask wallet address.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Get Deposit CLI

```
cd $HOME &&\
wget https://github.com/stratisproject/staking-deposit-cli/releases/download/0.1.0/staking-deposit-cli-linux-amd64.zip &&\
unzip staking-deposit-cli-linux-amd64.zip &&\
tar -C ~/go/bin -xzf staking-deposit-cli-linux-amd64/staking-deposit-cli-linux-amd64.tar.gz &&\
rm -rf staking-deposit-cli-linux-amd64 staking-deposit-cli-linux-amd64.zi


# Generate keys and deposit-data
deposit new-mnemonic --num_validators 1 --chain auroria --eth1_withdrawal_address [YOUR_WALLET_ADDRESS]

```

Follow the instructions , You will be asked to **create a password** and make sure to copy the **seed phrase**.

Will create a `validator_keys` folder and contains two files namely `deposit_data-*.json`, `keystore-m_*.json`.

Dont Forget Backup First Folder Validator\_Keys

Generate Validator CLI

```
cd $HOME &&\
wget https://github.com/stratisproject/prysm-stratis/releases/download/0.1.1/validator-linux-amd64-0ebd251.tar.gz &&\
tar -xzf validator-linux-amd64-0ebd251.tar.gz &&\
rm -rf validator-linux-amd64-0ebd251.tar.gz &&\
mv ./validator ~/go/bin 

#Import Validator Key
validator accounts import --keys-dir=/home/stratis/validator_keys/keystore-m*.json --auroria

```

```
Check location Validator Key after Import

```

```
cd /home/stratis/.eth2validators/prysm-wallet-v2

```

if found folder direct , your validator key succesfully .

Download or copy your `validator_keys` folder to local. then Deposit

* Claim testnet token [https://auroria.faucet.stratisevm.com/](https://auroria.faucet.stratisevm.com/)
* Click continue on [https://auroria.launchpad.stratisevm.com/en/generate-keys](https://auroria.launchpad.stratisevm.com/en/generate-keys)

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

* Upload deposit `deposit_data-xxx.json`
* Connect wallet , Click Continue and Confirm tx .
* Check tx hash : [https://auroria.beacon.stratisevm.com/tx/\[tx\_hash\]#overview](https://auroria.beacon.stratisevm.com/tx/\[tx_hash]#overview) .

Check Deposit Progress with Check your pubkey

```
cat ./validator_keys/deposit* | jq '.[].pubkey'
```

track deposit progress\
Go to [https://auroria.beacon.stratisevm.com/validator/\[YOUR\_PUBKEY](https://auroria.beacon.stratisevm.com/validator/\[YOUR_PUBKEY)] .\
It looks like this

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

It can take up to 16 hours to deposit in the beacon-chain process and your validator become Active .

Run Validator

Create Password File\
Change **“YOUR\_WALLET\_PASSWORD”** with your password

```
echo "YOUR_WALLET_PASSWORD" >> ./validator_keys/pwd.txt

# Create service file
# Change --suggested-fee-recipient=YOUR_ADDRESS with you address

sudo tee /etc/systemd/system/validator.service > /dev/null <<EOF
[Unit]
Description=validator node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which validator) --auroria --datadir=/root/.validator --wallet-password-file=/root/validator_keys/pwd.txt --wallet-dir=/root/.eth2validators/prysm-wallet-v2/ --suggested-fee-recipient=YOUR_ADDRESS --accept-terms-of-use
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

# Run validator

sudo systemctl daemon-reload
sudo systemctl enable validator
sudo systemctl restart validator
journalctl -fu validator -o cat
```

Be sure to keep your node validator running .

Once the deposit is confirmed and the validator is active. You will see different logs from the validator node as well on [https://auroria.beacon.stratisevm.com/validator/\[YOUR\_PUBKEY](https://auroria.beacon.stratisevm.com/validator/\[YOUR_PUBKEY)].

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

