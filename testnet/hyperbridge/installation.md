---
description: >-
  DISCLAIMER: THIS I CAN RUN USING CONTABO VPS, YOU NEED TO PAY ATTENTION TO THE
  PATH NAMING SECTION OF YOUR RESPECTIVE HOME FOLDERS
---

# 🖥️ Installation

Install dependencies

```
sudo apt install --assume-yes git clang curl libssl-dev llvm libudev-dev make protobuf-compiler
```

Install Rust Compiler

```

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

```

Install WebAssembly target

```
rustup update nightly
rustup target add wasm32-unknown-unknown 
```

Clone the repo

```
git clone https://github.com/polytope-labs/hyperbridge.git
cd ./hyperbidge
git checkout v0.3.5
```

Build the node&#x20;

```
cargo build --release -p hyperbridge
```

COMMMAND CREATE SYSTEMD

```
sudo tee /etc/systemd/system/hyperbridge.service > /dev/null <<EOF
[Unit]
Description="hyperbridge daemon node"
After=network-online.target

[Service]
User=$USER
Restart=on-failure
RestartSec=10
ExecStart=$HOME/hyperbridge/target/release/hyperbridge \
--base-path=$HOME/hyperbridge \
--pruning=archive \
--name="YOUR MONIKOR"  \
--rpc-cors=all \
--rpc-port=9944 \
--unsafe-rpc-external \
--rpc-methods=unsafe \
--chain=gargantua \
--no-mdns \
--listen-addr=/ip4/0.0.0.0/tcp/30333 \
--listen-addr=/ip6/::/tcp/30333 \
--public-addr=/ip4/your-node-ip-address/tcp/30333 \
--out-peers=32

[Install]
WantedBy=multi-user.target
EOF
```

run service

```
systemctl enable hyperbridge.service && \
systemctl start hyperbridge.service
```

check status

```
systemctl status hyperbridge.service
```

check log

```
journalctl -f -u hyperbridge.service
```







