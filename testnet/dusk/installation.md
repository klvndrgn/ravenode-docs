---
description: Incentivized Testnet by DUSK Network (ITN Node Running)
---

# 🖥️ Installation

## **Hardware Specifications**

<table data-full-width="false"><thead><tr><th width="208">CPU</th><th width="109">RAM</th><th width="95">Storage</th><th>Network Connection</th></tr></thead><tbody><tr><td>2 cores; 2 GHz</td><td>4 GB</td><td>50 GB</td><td>10 Mbps</td></tr></tbody></table>

## **Prerequisites**

```
Rust 1.71 nightly or higher
GCC 13 or higher
Clang 16 or higher
```

## Setting up the Environment

Open a terminal and run the following command to check if Rust is available:

```
rustc --version
```

If this returns a command not found error, we’ll need to install and activate Rust. Run the following command to download and install Rust:

{% code title="# Download and Install" %}
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs/ | sh
```
{% endcode %}

{% code title="# Activate rust" %}
```
source $HOME/.cargo/env
```
{% endcode %}

Install Clang

```
sudo apt-get install clang
```

Install Git

```
sudo apt-get install git
```

We’ve created an easy to use ITN installer. This installer will set up Rusk as a service on your droplet, preconfigure parts of the node, and provide a couple of helper scripts.

Install Rusk by pasting the following command in your droplet terminal:

```
curl --proto '=https' --tlsv1.2 -sSfL https://github.com/dusk-network/itn-installer/releases/download/v0.1.0/itn-installer.sh | sudo sh
```

## Configure Rusk

Once everything has been set up, you will be asked to add your consensus keys. These keys are used to sign and vote for blocks.

If you haven’t made a wallet yet, go to Dusk [Web Wallet](https://wallet.dusk.network/setup/) and create a new wallet. You can request funds from their [faucet here](https://faucet.dusk.network/). The faucet will give you 1100 tDUSK. The minimum to stake is 1000 tDUSK.

Once you have access to a Dusk mnemonic, run the following command:

```
rusk-wallet restore
```

You will be asked to provide your recovery phrase/mnemonic, **in lower caps**, and to enter a password for the wallet.

Once you’ve done so, run the following command to export a consensus key for the given wallet:

```
rusk-wallet export -d /opt/dusk/conf -n consensus.keys
```

You will be asked to set an encryption password for the consensus key. Remember it and run the following script to set it as an environment variable:

```
sh /opt/dusk/bin/setup_consensus_pwd.sh
```

If you’ve configured everything correctly, you can now start rusk:

```
service rusk start
```

Your node will now start syncing. You can check if it indeed is by running:

```
grep "block accepted" /var/log/rusk.log
```

Wait until your node is synced up. You can find the latest block height on [explorer](https://explorer.dusk.network/).

## Stake tDUSK

The final step is to stake. Once you received the faucet, you can stake by running:

```
rusk-wallet stake --amt 1000 # Or however much you want to stake
```

Once the transaction has gone through, you can view your staking information by running:

```
rusk-wallet stake-info
```

To see if your node is participating in consensus and creating blocks:

```
tail -F /var/log/rusk.log
```

Link to official docs: [https://docs.dusk.network/itn/node-running-guide/](https://docs.dusk.network/itn/node-running-guide/)
