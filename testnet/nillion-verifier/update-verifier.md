---
description: >-
  If you're running the Nillion Verifier, please update to the latest image
  version v1.0.1.
---

# 🖥️ Update Verifier

## List Available Docker Container

```
docker images
```

## Delete old Docker Images to free up disk space

```
docker rmi <image_id>
```

## Pull the latest accuser Image

```
docker pull nillion/retailtoken-accuser:v1.0.1
```

## Stop & remove the old container

```
docker ps
docker stop <containerid> && docker rm <containerid>
```

{% hint style="warning" %}
Change **\<containerid>** with your docker container id
{% endhint %}

## Restart the verifier

```
docker run -d -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.1 accuse --rpc-endpoint "https://testnet-nillion-rpc.lavenderfive.com/" --block-start 5040475
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

# View and filter the logs
docker logs -f $(docker ps | grep nillion | awk '{print $NF}') --tail 100 | grep -E "Registered:|Secret"
```

{% hint style="info" %}
Wait until it synchronizes with the current block height. Once it does, you should see if the status shows "Registered" as true.
{% endhint %}

If the issue occurs again, you can restart it by running this command:

```
docker ps -a | grep nillion | awk '{print $1}' | xargs -I {} docker stop {} && \
docker ps -a | grep nillion | awk '{print $1}' | xargs -I {} docker rm {} && \
LATEST_BLOCK=$(curl -s https://testnet-nillion-rpc.lavenderfive.com/status | jq -r .result.sync_info.latest_block_height) && \
docker run -d -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.1 accuse --rpc-endpoint "https://testnet-nillion-rpc.lavenderfive.com" --block-start "$LATEST_BLOCK" && \
docker logs -f $(docker ps | grep nillion | awk '{print $NF}')
```
