# 🖥️ Installation

## **Hardware Specifications**

<table><thead><tr><th width="320">CPU</th><th width="156">RAM</th><th>Storage</th><th>Network Port</th></tr></thead><tbody><tr><td>Intel(R) Core(TM) i7-7700K CPU @ 4.20GHz</td><td>32 GB (Recommended)</td><td>500 GB NVMe</td><td>30333 TCP</td></tr><tr><td>Any 4 vCPU Cores should run</td><td>4 GB (Minimum)</td><td>500 GB SSD</td><td>30333 TCP</td></tr></tbody></table>

## Install Dependencies <a href="#install-dependencies" id="install-dependencies"></a>

<pre><code><strong>sudo apt install --assume-yes git clang curl libssl-dev llvm libudev-dev make protobuf-compiler
</strong></code></pre>

### Open Port

```
sudo ufw allow 22; sudo ufw allow 30333
```

## Download Binary & Copy to /usr/bin <a href="#download-binary--copy-to-usrbin" id="download-binary--copy-to-usrbin"></a>

```
mkdir -p $HOME/.tangle
wget -O tangle https://github.com/webb-tools/tangle/releases/download/v0.6.1/tangle-testnet-linux-amd64 && chmod +x tangle
sudo cp tangle /usr/bin/
```

## Create Daemon Service

```
sudo tee /etc/systemd/system/tangle.service > /dev/null << EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
ExecStart=/usr/bin/tangle \
  --base-path $HOME/.tangle/data/validator/YourMoniker \
  --name YourName \
  --chain tangle-testnet \
  --auto-insert-keys \
  --node-key-file "/home/YourMoniker/node-key" \
  --port 30333 \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
  --validator \
  --no-mdns
[Install]
WantedBy=multi-user.target
EOF
```

{% hint style="warning" %}
Change all **YourMoniker** to your name
{% endhint %}

## Start Tangle service

```
sudo systemctl daemon-reload
sudo systemctl enable tangle
sudo systemctl start tangle
```

## Check logs

```
sudo journalctl -u tangle -f --no-hostname -o cat
```

* Example Output

```
2024-02-23 22:44:16 🔍 Discovered new external address for our node: /ip4/84.247.146.186/tcp/30333/p2p/12D3KooWJBdKT2KB6CaUe37amdrW4tJaFajHhgmK34uiUnBFxwyv
2024-02-23 22:44:20 ⚙️  Syncing, target=#605047 (6 peers), best: #384 (0x2039…5ae2), finalized #1 (0x26f1…77b1), ⬇ 119.0kiB/s ⬆ 14.0kiB/s
2024-02-23 22:44:25 ⚙️  Syncing 228.6 bps, target=#605048 (6 peers), best: #1531 (0xa961…fc3d), finalized #1024 (0x6121…001b), ⬇ 102.9kiB/s ⬆ 6.2kiB/s
2024-02-23 22:44:30 ⚙️  Syncing 230.9 bps, target=#605048 (6 peers), best: #2687 (0xdba9…e348), finalized #2560 (0x9890…58a1), ⬇ 79.9kiB/s ⬆ 6.4kiB/s
2024-02-23 22:44:35 ⚙️  Syncing 232.2 bps, target=#605049 (6 peers), best: #3848 (0xb3ed…a1f5), finalized #3584 (0xfc0f…e3d6), ⬇ 106.8kiB/s ⬆ 1.3kiB/s
2024-02-23 22:44:40 ⚙️  Syncing 253.9 bps, target=#605049 (6 peers), best: #5120 (0xf2d9…90f5), finalized #4608 (0xe3af…52bb), ⬇ 58.4kiB/s ⬆ 0.7kiB/s
2024-02-23 22:44:45 ⚙️  Syncing 229.2 bps, target=#605049 (7 peers), best: #6268 (0x7634…8a02), finalized #6144 (0x48ed…9043), ⬇ 81.6kiB/s ⬆ 0.9kiB/s
2024-02-23 22:44:50 ⚙️  Syncing 248.7 bps, target=#605049 (7 peers), best: #7514 (0x158c…7055), finalized #7168 (0xca31…eefe), ⬇ 79.5kiB/s ⬆ 1.2kiB/s
2024-02-23 22:44:55 ⚙️  Syncing 227.8 bps, target=#605050 (7 peers), best: #8653 (0xbcd3…3a40), finalized #8192 (0x97b3…033f), ⬇ 55.9kiB/s ⬆ 1.0kiB/s
2024-02-23 22:45:00 ⚙️  Syncing 215.0 bps, target=#605050 (7 peers), best: #9728 (0x3a6e…9b74), finalized #9216 (0xdc44…4e7f), ⬇ 96.1kiB/s ⬆ 0.8kiB/s
2024-02-23 22:45:05 ⚙️  Syncing 157.4 bps, target=#605051 (7 peers), best: #10515 (0xace8…997f), finalized #10240 (0xb608…0036), ⬇ 44.8kiB/s ⬆ 1.2kiB/s
2024-02-23 22:45:06 [10778] 💸 generated 22 npos targets
```

## Bond TNT and setup validator Account <a href="#bond-tnt-and-setup-validator-account" id="bond-tnt-and-setup-validator-account"></a>

* Go to [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet-rpc.tangle.tools#/accounts)
* Create new  account 1 for Stash and 1 for Controller

<div data-full-width="false"><figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Click <strong>"Stash"</strong> button</p></figcaption></figure></div>

* Choose **Stash** & **Controller** account, then fill the amount you want to stake

## Generate Session Key

```
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9944
```

* Copy the `0x` Output
* Back to [PolkadotJS](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet-rpc.tangle.tools#/accounts)
* Click **Set Session Key**
* Input the `0x` Output before
* Validator created successfully
* Check if your validator is on the [telemetry](https://telemetry.polkadot.io/#stats/0x3d22af97d919611e03bbcbda96f65988758865423e89b2d99547a6bb61452db3)

{% hint style="danger" %}
If you want to delete Tangle service, run this command below:
{% endhint %}

```
sudo systemctl stop tangle
sudo systemctl disable tangle
rm -r /usr/bin/tangle
cd $HOME
rm -r .tangle
```
