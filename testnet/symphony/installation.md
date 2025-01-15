# 🖥️ Installation

## **Hardware Specifications**

<table><thead><tr><th width="320">CPU</th><th width="156">RAM</th><th>Storage</th></tr></thead><tbody><tr><td>6 or more physical CPU cores</td><td>16 GB</td><td>1 TB SSD</td></tr></tbody></table>

## Install Dependencies <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```

### Configure Moniker

```
MONIKER="<your-node-name>"
```

### Install GO

```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.2.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

### Download and Build Binaries

```
# Clone project repository
cd $HOME
rm -rf symphony
git clone https://github.com/Orchestra-Labs/symphony
cd symphony
git checkout v0.4.1

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.symphonyd/cosmovisor/genesis/bin
mv build/symphonyd $HOME/.symphonyd/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.symphonyd/cosmovisor/genesis $HOME/.symphonyd/cosmovisor/current -f
sudo ln -s $HOME/.symphonyd/cosmovisor/current/bin/symphonyd /usr/local/bin/symphonyd -f
```

### Install Cosmovisor and create daemon service

```
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.6.0

# Create service
sudo tee /etc/systemd/system/symphony.service > /dev/null << EOF
[Unit]
Description=symphony node service
After=network-online.target
 
[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.symphonyd"
Environment="DAEMON_NAME=symphonyd"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.symphonyd/cosmovisor/current/bin"
 
[Install]
WantedBy=multi-user.target
EOF

# Enable Service
sudo systemctl daemon-reload
sudo systemctl enable symphony.service
```

## Initialize the node

```
# Set node configuration
symphonyd config chain-id symphony-testnet-4
symphonyd config keyring-backend test

# Initialize the node
symphonyd init $MONIKER --chain-id symphony-testnet-4

# Download genesis and addrbook
curl -Ls https://snapshot.ravenode.xyz/symphony/genesis.json > $HOME/.symphonyd/config/genesis.json
curl -Ls https://snapshot.ravenode.xyz/symphony/addrbook.json > $HOME/.symphonyd/config/addrbook.json

# Add seeds
PEERS="$(curl -sS https://rpc.symphony.ravenode.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.symphonyd/config/config.toml

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0note\"|" $HOME/.symphonyd/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.symphonyd/config/app.toml
```

## Download Latest Snapshot & extract the file

```
curl -L https://snapshot.ravenode.xyz/symphony/symphony-latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.symphonyd
[[ -f $HOME/.symphonyd/data/upgrade-info.json ]] && cp $HOME/.symphonyd/data/upgrade-info.json $HOME/.symphonyd/cosmovisor/genesis/upgrade-info.json
```

## Start Service & Check Node Logs

```
sudo systemctl restart symphony.service && sudo journalctl -u symphony.service -f --no-hostname -o cat
```

## Verify node's block height sync

```
while true; do
  local_height=$(symphonyd status 2>&1 | jq -r .sync_info.latest_block_height || echo "0")

  network_height=$(curl -s https://rpc.symphony.ravenode.xyz/status | jq -r .result.sync_info.latest_block_height || echo "0")

  blocks_left=$((network_height - local_height))

  n_peers=$(curl -Ss localhost:26657/net_info | jq -r .result.n_peers || echo "0")

  echo "Curr block: $local_height (blocks left: $blocks_left) | Peers: $n_peers"

  sleep 3
done
```

* Example Output

```
Curr block: 194935 (blocks left: 1) | Peers: 47
Curr block: 194936 (blocks left: 0) | Peers: 47
Curr block: 194937 (blocks left: 0) | Peers: 47
```

{% hint style="info" %}
If you receive output like the above and "blocks left" is 0, it means the node is fully synced and has reached the latest block height. The node is now up to date with the blockchain and in sync with the other peers.
{% endhint %}

## Spin Up Validator

### 1. Create a wallet

You have two options for that.

**OPTION 1 - CREATE NEW WALLET**

```
symphonyd keys add wallet
```

{% hint style="danger" %}
Save the mnemonic output as this is the only way to recover your validator wallet in case you lose it!
{% endhint %}

**OPTION 2 - RECOVER EXISTING WALLET**

```
symphonyd keys add wallet --recover
```

To list your wallets use command below

```
symphonyd keys list
```

### 2. Fund a wallet

To create a validator, you need to fund the previously created wallet using [https://testnet.ping.pub/symphony/faucet](https://testnet.ping.pub/symphony/faucet)

To check wallet balance use command below

```
symphonyd q bank balances $(symphonyd keys show wallet -a)
```

### 3.  Create Validator

Use the following command to get the your validator `pubkey` value:

<pre><code><strong>symphonyd tendermint show-validator
</strong></code></pre>

Create a `validator.json` file inside `/root/.symphonyd/`

```
nano /root/.symphonyd/validator.json
```

Copy the following command and paste it into the `validator.json` file:

```
{
    "pubkey": {
        "@type": "/cosmos.crypto.ed25519.PubKey",
        "key": "your_validator_pubkey"
    },
    "amount": "1000000note",
    "moniker": "your-moniker-name",
    "identity": "your-keybase-id",
    "website": "your-website",
    "details": "your-details",
    "commission-rate": "0.05",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
```

{% hint style="warning" %}
Please make sure you have adjusted **key,** **moniker**, **identity**, **details**, **website** to match your values.
{% endhint %}

Run the create validator command:

```
symphonyd tx staking create-validator $HOME/.symphonyd/validator.json \
--from=wallet \
--chain-id=symphony-testnet-4 \
--gas-adjustment 1.5 \
--gas-prices 0.025note \
--gas auto
```

{% hint style="danger" %}
Save the \~/.symphonyd/config/priv\_validator\_key.json file as this is the only way to recover your validator signing key in case you lose it!
{% endhint %}

## Fill Validator Form

Once your node is synced and you've successfully created a validator, please fill out this form to receive additional faucet tokens for validator bonding: [https://forms.gle/98ajH8wXCowVnGZi6](https://forms.gle/98ajH8wXCowVnGZi6)

Join Symphony [Discord](https://discord.gg/gRTXEGrc) and receive **Validator** role, submit your validator link and tag the team.

<figure><img src="../../.gitbook/assets/HCA logo.jpg" alt="" width="160"><figcaption></figcaption></figure>

{% embed url="https://t.me/HappyCuanAirdrop" %}
Join our Telegram group for the latest updates and discussions!
{% endembed %}
