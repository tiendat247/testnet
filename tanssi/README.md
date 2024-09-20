# Tanssi SystemD

Tanssi, an appchain infrastructure protocol, is currently under construction to simplify and enhance appchain deployment like never before.

### Website: https://www.tanssi.network

### Telegram (Tanssi Network Official): https://t.me/tanssiofficial 

### Telegram (Tanssi Vietnam): https://t.me/tanssivietnam

### Twitter: https://twitter.com/TanssiNetwork

### Discord: https://discord.gg/zrHsyVUnrR

### Docs: https://docs.tanssi.network

### Explorer: https://polkadot.js.org/apps/?rpc=wss://fraa-dancebox-rpc.a.dancebox.tanssi.network

### Check telemetry: https://telemetry.polkadot.io/#list/0x27aafd88e5921f5d5c6aebcd728dacbbf5c2a37f63e2eda301f8e0def01c43ea

### SubScan: https://dancebox.subscan.io

### Staking: https://apps.tanssi.network/staking

### Fill form: https://www.tanssi.network/testnet-campaign/block-producers-waitlist 

## Recommended Hardware Requirements 

|   SPEC	    |         FullNode          |       Block Producer      |
| :---------: | :-----------------------: |:-----------------------:  |    
|   **CPU**   |        ≥ 4 Cores          |        ≥ 8 Cores          |
|   **RAM**   |        ≥ 8 GB             |        ≥ 32 GB            |
| **Storage** |        ≥ 500 GB SSD       |        ≥ 1 TB SSD         |
| **NETWORK** |        ≥ 100 Mbps         |        ≥ 500 Mbps         |

## 1. Update system and install build tools
```
apt update && apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y
```
## 2. Download the Latest Release
```
wget https://github.com/moondance-labs/tanssi/releases/download/v0.8.1/tanssi-node && \
chmod +x ./tanssi-node
```
## 3. Creat new wallet
```
./tanssi-node key generate -w24
```
## 4. Creat tanssi-data
```
cd
mkdir tanssi-data
mv tanssi-node ~/tanssi-data
```
## 5. Create the Systemd Service Configuration File
(**Replace** _INSERT_YOUR_TANSSI_NODE_NAME_)
Creat tanssid.service & save file
```
sudo nano /etc/systemd/system/tanssid.service
```
```
[Unit]
Description="Tanssi systemd service"
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=on-failure
RestartSec=10
User=root
SyslogIdentifier=tanssi
SyslogFacility=local7
KillSignal=SIGHUP
ExecStart=/root/tanssi-data/tanssi-node \
--chain=dancebox \
--name=INSERT_YOUR_TANSSI_NODE_NAME \
--unsafe-force-node-key-generation \
--sync=warp \
--base-path=/root/tanssi-data/para \
--state-pruning=2000 \
--blocks-pruning=2000 \
--collator \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb \
-- \
--name=INSERT_YOUR_TANSSI_NODE_NAME \
--base-path=/root/tanssi-data/container \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
-- \
--chain=westend_moonbase_relay_testnet \
--name=INSERT_YOUR_TANSSI_NODE_NAME \
--unsafe-force-node-key-generation \
--sync=fast \
--base-path=/root/tanssi-data/relay \
--state-pruning=2000 \
--blocks-pruning=2000 \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb \

[Install]
WantedBy=multi-user.target
```
```
systemctl enable tanssi.service
systemctl daemon-reload
```
```
systemctl restart tanssid && journalctl -fu tanssid
```

## Generate Session Keys
```
curl http://127.0.0.1:9944 -H \
"Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"author_rotateKeys",
    "params": []
  }'
```
