# 🖥️ Installation

## Prerequisites

{% code fullWidth="false" %}
```
1. install docker-compose & git 
2. Get Ethereum Sepolia WebSocket endpoint via Infura.io (https://app.infura.io/)
3. Get Ethereum Sepolia Faucet minimum <0.01 ETH (https://www.infura.io/faucet/sepolia)
4. EVM Compatible Wallet Address
```
{% endcode %}

## Install `docker-compose`

1. Download the Docker Compose binary into the `/usr/local/bin` directory:

```
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. Verify that Docker Compose is installed correctly:

```
docker-compose --version
```

3. Apply executable permissions to the Docker Compose binary:

```
sudo chmod +x /usr/local/bin/docker-compose
```

## Install Git

```
sudo apt-get install git
```

## Clone `nwaku-compose` repo

{% code fullWidth="false" %}
```
git clone https://github.com/waku-org/nwaku-compose.git
```
{% endcode %}

Navigate to `nwaku-compose` there is `.env.example` available for you as a template to use for providing the below values.

`docker-compose` will read the `./.env` file from the filesystem. The process when working with `.env` files is to copy the .env.example, store it as .env and edit the values there by running the following command:

```
cp .env.example .env
```

Edit the .env value with your own data

{% code fullWidth="false" %}
```
vim .env
```
{% endcode %}

{% code title="Insert the required data" overflow="wrap" fullWidth="false" %}
```
ETH_CLIENT_ADDRESS=wss://sepolia.infura.io/ws/v3/<YOUR_ENDPOINT_KEY_HERE>  #RPC URL for accessing testnet via websockets.
ETH_TESTNET_KEY=<YOUR_PRIVATE_KEY_HERE>  # Privatekey of testnet where you have sepolia ETH that would be staked into RLN contract (If you can't find one, export private key from your EVM wallet address)
RLN_RELAY_CRED_PASSWORD="<YOUR_CUSTOM_PASSWORD_HERE>" #Password you would like to use to protect your RLN membership

# Advanced (Optional)
NWAKU_IMAGE=
NODEKEY=
DOMAIN=
EXTRA_ARGS=
RLN_RELAY_CONTRACT_ADDRESS=
```
{% endcode %}

## Register RLN membership

This command will register your membership and store it in `keystore/keystore.json`. Note that if you just want to relay traffic (not publish), you don't need one.

{% code fullWidth="false" %}
```
./register_rln.sh
```
{% endcode %}

## Start the node

Start all processes: nwaku node, database and grafana for metrics. Your RLN membership is loaded into nwaku under the hood.

{% code fullWidth="false" %}
```
docker-compose up -d
```
{% endcode %}

Your nwaku node exposes a **REST API** to interact with it.

{% code fullWidth="false" %}
```
# get nwaku version
curl http://127.0.0.1:8645/debug/v1/version

# get nwaku info
curl http://127.0.0.1:8645/debug/v1/info
```
{% endcode %}

Publish a message to a contentTopic. Everyone subscribed to it will receive it. Note that payload is base64 encoded.

{% code fullWidth="false" %}
```
curl -X POST "http://127.0.0.1:8645/relay/v1/auto/messages"  
-H "content-type: application/json"  
-d '{"payload":"'$(echo -n "Hello Waku Network - from Anonymous User" | base64)'","contentTopic":"/my-app/2/chatroom-1/proto","timestamp":'$(date +%s%N)'}'
```
{% endcode %}

Get messages sent to a contentTopic. Note that any store node in the network is used to reply.

{% code fullWidth="false" %}
```
curl -X GET "http://127.0.0.1:8645/store/v1/messages?contentTopics=%2Fmy-app%2F2%2Fchatroom-1%2Fproto&pageSize=50&ascending=true" \
 -H "accept: application/json"
```
{% endcode %}

## Grafana Node monitoring

```
Change in docker-compose.yml 127.0.0.0:3000 for 0.0.0.0:3000 and open the port 3000 in the remote machine where you're running your node.

After that you can access it at http://localhost:3000/d/yns_4vFVk/nwaku-monitoring for node metrics
```

Link to official docs : [https://github.com/waku-org/nwaku-compose/blob/master/README.md](https://github.com/waku-org/nwaku-compose/blob/master/README.md)

{% code fullWidth="true" %}
```
```
{% endcode %}
