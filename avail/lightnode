
# Avail Light Node

*System: Ubuntu 22.04*

## 

**Server preparation**

```
sudo apt update
sudo apt install wget curl make clang pkg-config libssl-dev build-essential -y
```

## 

**Download the binary file**

```
sudo mkdir -p $HOME/avail-light && cd $HOME/avail-light
sudo wget https://github.com/availproject/avail-light/releases/download/v1.10.2/avail-light-linux-amd64.tar.gz
sudo tar xvzf avail-light-linux-amd64.tar.gz && mv avail-light-linux-amd64 avail-light && cp avail-light /usr/bin /usr/local/bin
```

```
sudo tee ${HOME}/avail-light/config1.toml > /dev/null << EOF
# config.yaml
log_level = "info"
http_server_host = "127.0.0.1"
http_server_port = 7000

secret_key = { seed = "avail" }
port = 37000

full_node_ws = ["ws://127.0.0.1:9944"]
app_id = 0
confidence = 92.0
avail_path = "avail_path"
bootstraps = ["/ip4/127.0.0.1/tcp/39000/p2p/12D3KooWMm1c4pzeLPGkkCJMAgFbsfQ8xmVDusg272icWsaNHWzN"]
EOF
```

## 

**Create a service**

```
sudo tee /etc/systemd/system/availd.service > /dev/null << EOF
[Unit]
Description=Avail Light Node
After=network.target
StartLimitIntervalSec=0
[Service]
User=$USER
Type=simple
Restart=always
RestartSec=120
ExecStart= avail-light --config ${HOME}/avail-light/config.yaml --identity ${HOME}/avail-light/identity.toml --network goldberg
[Install]
WantedBy=multi-user.target
EOF
```

Change your identity by your seed phrase in **identity.toml**

```
sudo systemctl daemon-reload
sudo systemctl enable availd
sudo systemctl restart availd && sudo journalctl -u availd -f -o cat
```
