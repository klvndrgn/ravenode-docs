---
description: Proceed carefully when initiating this deletion process
---

# ⚠️ Timechain Node Deletion

## List Available Docker Container

```
docker ps -a
```

## Stop Analog Docker

```
docker stop analog
```

## Delete Analog Docker Container

```
docker rm -f analog
```

## Remove the Analog Docker Image

```
docker rmi analoglabs/timechain
```

## Remove Directory (Optional)

```
rm -rf $(pwd)/.analog
```
