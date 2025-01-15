---
description: >-
  If you are running Analog, check if your node is stuck at block height:
  1,299,707
---

# 🖥️ Update Timechain Node

## Download the snapshot

```
curl -O https://analog-public.s3.amazonaws.com/backup/testnet-backup.tar.gz
```

## Pull the latest Analog Docker Image

```
docker pull analoglabs/timenode-test:latest
```

## Stop the node

```
docker ps
docker stop CONTAINERID && docker rm CONTAINERID
```

{% hint style="danger" %}
Change **CONTAINERID** with your docker container id
{% endhint %}

## Delete the old Analog node database folder

```
cd .analog
rm -rf ./chains/analogcc1/paritydb/full
```

## Extract the snapshot file

Make sure you are in the /.analog path directory before running this command

```
tar -xvzf ~/testnet-backup.tar.gz -C .
```

## Restart the node

```
docker run -d --name analog -p 9944:9944 -p 30303:30303 -v $(pwd)/.analog:/.analog analoglabs/timenode-test \
    --base-path /.analog \
    --rpc-external \
    --unsafe-rpc-external \
    --rpc-cors all \
    --name YourMoniker \
    --telemetry-url="wss://telemetry.analog.one/submit 9" \
    --rpc-methods Unsafe
```

{% hint style="danger" %}
Change **YourMoniker** to your node name
{% endhint %}

## Check node logs

```
docker logs -f analog
```

If you see an error in the initial logs like below, just wait until it syncs normally again:

{% hint style="info" %}
2024-07-16 05:14:01 panicked at /build/runtime/src/lib.rs:1050:1: Bad input data provided to validate\_transaction: Codec error
{% endhint %}

## Generate new session key and update it on the Analog Polkadot.js Portal

```
echo '{"id":1,"jsonrpc":"2.0","method":"author_rotateKeys","params":[]}' | websocat -n1 -B 99999999 ws://127.0.0.1:9944
```
