# 🖥️ Installation

Getting the Docker Image

To get the `uniond` image, you can visit [our container](https://github.com/orgs/unionlabs/packages/container/package/uniond-release) on the GitHub Container Registry, or run the following command:

```
docker pull ghcr.io/unionlabs/uniond-release:v0.19.0
```

Running uniond

Creating a Chain Config & State Folder Before running this image, we need to create a folder to host the chain configuration and state. You can create this wherever you would like, but we’ll be doing so in our current user’s home directory.

To create a directory for `uniond` in your user home directory, run:

```
mkdir ~/.union
```

First, set some environment variables, which are used throughout initialization.\
FOR MONIKER AND KEY NAME customize to your needs

{% code title="" %}
```
export CHAIN_ID=union-testnet-6
export MONIKER="Unionized Goblin"
export KEY_NAME=alice
export GENESIS_URL="union.testnet.6.seed.poisonphang.com:26657/genesis"
```
{% endcode %}

Initializing the Chain Config & State Folder Now, using the uniond image and the folder we just created, we can initialize the contents of this folder. To do this, we’ll be using Docker volumes.

```
docker run \
  --user $(id -u):$(id -g) \
  --volume ~/.union:/.union \
  --volume /tmp:/tmp \
  -it ghcr.io/unionlabs/uniond-release:v0.19.0 init $MONIKER \
  --home /.union
```

after docker run , union succesfully install , run this command for init uniond

```
uniond init $MONIKER --chain-id $CHAIN_ID
```

Dont forget Modify Seeds,

Next, edit `~/.union/config/config.toml`. We’ll set the seeds to ensure your node can connect to the peer-to-peer network.

For `union-testnet-6` replace `seeds = ""` with:

```
seeds = "b37de4c50e26f7cde4c7b6ce06046a6693ffef2c@union.testnet.6.seed.poisonphang.com:26656"
```

and dont forget download genesis.json , download genesis json from this link\
[https://union.build/genesis.json](https://union.build/genesis.json)\
Download the `genesis.json` and copy it to your `uniond` home directory.

First, add a wallet that holds Union tokens. To create a new Account and mnemonic, use the following sub-command of the `uniond` binary.

```
uniond keys add $KEY_NAME
```

check if account union successfully registered

```
uniond keys show $KEY_NAME --address
```

To run `uniond` sub-commands, it will be useful to alias the Docker command in your shell `.*rc` file.

For example, in `zsh`, you can add the following alias to your `.zshrc`:

```
alias uniond='docker run -v ~/.union:/.union -v /tmp:/tmp --network host -it ghcr.io/unionlabs/uniond-release:$UNIOND_VERSION --home /.union'
```

To run the node, the .union file is required to be installed, then create a compose yml script, copy the yml script below then run the yml script

```
services:
  node:
    image: ghcr.io/unionlabs/uniond-release:${UNIOND_VERSION}
    volumes:
      - ~/.union:/.union
      - /tmp:/tmp
    network_mode: "host"
    restart: unless-stopped
    command: start --home /.union
```

To run your node in detached mode, run:

```
docker compose --file path/to/compose.yaml up --detach
```

Link to official docs : [https://union.build/docs/joining-testnet/getting-started/](https://union.build/docs/joining-testnet/getting-started/)

