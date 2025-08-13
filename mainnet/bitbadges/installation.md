# 🖥️ Installation

## **Hardware Specifications**

<table><thead><tr><th width="320">CPU</th><th width="156">RAM</th><th>Storage</th></tr></thead><tbody><tr><td>6 cores</td><td>16 GB</td><td>500 GB SSD</td></tr></tbody></table>

## Install Dependencies <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt update && sudo apt upgrade -y && sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

### Configure Moniker

```
MONIKER="<your-node-name>"
```

### Install GO

If you already have GO installed you can skip this

```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.2.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```

### Download and Install Binaries

```
# Clone project repository
cd $HOME
rm -rf bitbadgeschain
git clone https://github.com/BitBadges/bitbadgeschain.git
cd bitbadgeschain
git checkout v12

# Build binaries
make build-linux/amd64
cp build/bitbadgeschain-linux-amd64 /usr/local/bin/bitbadgeschaind
chmod +x /usr/local/bin/bitbadgeschaind
```

### Download libwasm

```
cd /usr/lib
sudo curl -L -o libwasmvm.x86_64.so https://github.com/CosmWasm/wasmvm/releases/download/v1.2.3/libwasmvm.x86_64.so
bash
sudo ldconfig
```

## Initialize the node

```
# Initialize the node
bitbadgeschaind init $MONIKER --chain-id bitbadges-1

# Download genesis and addrbook
curl -Ls https://snapshot.ravenode.xyz/bitbadges/genesis.json > $HOME/.bitbadgeschain/config/genesis.json
curl -Ls https://snapshot.ravenode.xyz/bitbadges/addrbook.json > $HOME/.bitbadgeschain/config/addrbook.json

# Add seeds
PEERS="$(curl -sS https://rpc.bitbadges.ravenode.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | sed -z 's|\n|,|g;s|.$||')"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.bitbadgeschain/config/config.toml

# Set minimum gas price
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025ubadge\"/" $HOME/.bitbadgeschain/config/app.toml

# Enable Prometheus
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.bitbadgeschain/config/config.toml

# Disable the Indexer
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.bitbadgeschain/config/config.toml

# Set pruning
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.bitbadgeschain/config/app.toml 
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.bitbadgeschain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"19\"/" $HOME/.bitbadgeschain/config/app.toml
```

## Create Service File

```
sudo tee /etc/systemd/system/bitbadgeschaind.service > /dev/null <<EOF
[Unit]
Description=BitBadges Mainnet Node
After=network-online.target

[Service]
User=root
ExecStart=/usr/local/bin/bitbadgeschaind start
Restart=always
RestartSec=3
LimitNOFILE=65535
Environment="LD_LIBRARY_PATH=/root/.bitbadgeschain/lib"

[Install]
WantedBy=multi-user.target
EOF
```

## Download Latest Snapshot & extract the file

```
sudo apt install lz4 -y
sudo systemctl stop bitbadgeschaind
bitbadgeschaind tendermint unsafe-reset-all --home $HOME/.bitbadgeschain --keep-addr-book
curl -L https://snapshot.ravenode.xyz/bitbadges/bitbadges-latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.bitbadgeschain
```

## Start Service & Check Node Logs

```
sudo systemctl daemon-reload && sudo systemctl enable bitbadgeschaind.service && sudo systemctl restart bitbadgeschaind.service && sudo journalctl -u bitbadgeschaind.service -f --no-hostname -o cat
```

## Verify node's block height sync

```
while true; do
  local_height=$(bitbadgeschaind status 2>&1 | jq -r .sync_info.latest_block_height || echo "0")

  network_height=$(curl -s https://rpc.bitbadges.ravenode.xyz/status | jq -r .result.sync_info.latest_block_height || echo "0")

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
bitbadgeschaind keys add wallet
```

{% hint style="danger" %}
Save the mnemonic output as this is the only way to recover your validator wallet in case you lose it!
{% endhint %}

**OPTION 2 - RECOVER EXISTING WALLET**

```
bitbadgeschaind keys add wallet --recover
```

To list your wallets use command below

```
bitbadgeschaind keys list
```

### 2. Fund a wallet

To create a validator, you need to fund the previously created wallet

To check wallet balance use command below

```
bitbadgeschaind q bank balances $(bitbadgeschaind keys show wallet -a)
```

### 3.  Create Validator

Use the following command to get the your validator `pubkey` value:

<pre><code><strong>bitbadgeschaind tendermint show-validator
</strong></code></pre>

Create a `validator.json` file inside `/root/.bitbadgeschain/`

```
nano /root/.bitbadgeschain/validator.json
```

Copy the following command and paste it into the `validator.json` file:

```
{
    "pubkey": {
        "@type": "/cosmos.crypto.ed25519.PubKey",
        "key": "your_validator_pubkey"
    },
    "amount": "1000000000ubadge",
    "moniker": "your-moniker-name",
    "identity": "your-keybase-id",
    "website": "your-website",
    "details": "your-details",
    "commission-rate": "0.10",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.10",
    "min-self-delegation": "1"
}
```

{% hint style="warning" %}
Please make sure you have adjusted **key,** **moniker**, **identity**, **details**, **website** to match your values.
{% endhint %}

Run the create validator command:

```
bitbadgeschaind tx staking create-validator $HOME/.bitbadgeschain/validator.json \
--from=wallet \
--chain-id=bitbadges-1 \
--gas-adjustment 1.5 \
--gas-prices 0.025ubadge \
--gas auto
```

{% hint style="danger" %}
Save the \~/.bitbadgeschain/config/priv\_validator\_key.json file as this is the only way to recover your validator signing key in case you lose it!
{% endhint %}
