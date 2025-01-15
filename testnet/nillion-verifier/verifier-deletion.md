---
description: Proceed carefully when initiating this deletion process
---

# ⚠️ Verifier Deletion

## List Available Docker Container

```
docker ps -a
```

## Stop Docker Container

```
docker stop [container_name_or_id]
```

## Delete Docker Container

```
docker rm -f [container_name_or_id]
```

## List Available Docker Images

```
docker images
```

## Remove Docker Image

```
docker rmi [container_name_or_id]
```

## Remove Directory (Optional)

```
rm -rf $(pwd)/nillion
```

## If you want to delete all & rerun with the latest block simply paste this code below

```
docker ps -a | grep nillion | awk '{print $1}' | xargs -I {} docker stop {} && \
docker ps -a | grep nillion | awk '{print $1}' | xargs -I {} docker rm {} && \
LATEST_BLOCK=$(curl -s https://testnet-nillion-rpc.lavenderfive.com/status | jq -r .result.sync_info.latest_block_height) && \
docker run -d -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.0 accuse --rpc-endpoint "https://testnet-nillion-rpc.lavenderfive.com" --block-start "$LATEST_BLOCK" && \
docker logs -f $(docker ps | grep nillion | awk '{print $NF}')
```
