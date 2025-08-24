---
description: >-
  This guide provides step-by-step instructions for setting up the Symphony
  Oracle Voter.
---

# 🖥️ Oracle Voter Setup

## Prerequisites

* Git
* Python 3 - Python3.11 Recommended
* Systemd (usually pre-installed on most Linux distributions)
* Sudo privileges
* Server running symphonyd with synced status

## Install Dependencies <a href="#install-dependencies" id="install-dependencies"></a>

```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
sudo apt-get update && apt-get install -y libssl-dev
```

### Navigate to the project directory and checkout current version

```
cd $HOME
git clone https://github.com/cmancrypto/symphony-oracle-voter.git
cd symphony-oracle-voter
git checkout v1.0.0
```

### Create a .env file

```
cp .env_sample .env
nano .env
```

Change the below format for .env identity

```
VALIDATOR_ADDRESS= symphonyvaloperxxx
VALIDATOR_ACC_ADDRESS = symphonyxxx
KEY_PASSWORD= keyringpassword
SYMPHONY_LCD = https://api.symphony.ravenode.xyz/
TENDERMINT_RPC = https://rpc.symphony.ravenode.xyz/
```

{% hint style="warning" %}
Adjust VALIDATOR\_ADDRESS, VALIDATOR\_ACC\_ADDRESS, and KEY\_PASSWORD with your own validator details.
{% endhint %}

## Create python virtual environment

```
python3 -m venv venv
```

## Activate the virtual environment

```
source venv/bin/activate
```

## Install the required packages

```
pip install -r requirements.txt
```

## Deactivate the virtual environment

```
deactivate
```

## Create oracle.service file

```
sudo tee /etc/systemd/system/oracle.service > /dev/null << EOF
[Unit]
Description=Symphony Oracle
After=network.target

[Service]
# Environment variables
Environment="SYMPHONYD_PATH=$(which symphonyd)"
Environment="PYTHON_ENV=production"
Environment="LOG_LEVEL=INFO"
Environment="DEBUG=false"

# Service configuration
Type=simple
User=root
WorkingDirectory=/root/symphony-oracle-voter/
ExecStart=/root/symphony-oracle-voter/venv/bin/python3 -u /root/symphony-oracle-voter/main.py
Restart=always
RestartSec=3
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
EOF
```

## Start oracle.service

```
sudo systemctl daemon-reload
sudo systemctl enable oracle.service
sudo systemctl start oracle.service
```

## Check oracle logs

```
sudo journalctl -u oracle -f -o cat
```
