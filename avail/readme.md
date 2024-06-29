
*The essential base layer for modern blockchains. With Avail, it’s never been easier to spin up your own blockchains*

### 

**Avail's Clash of Nodes Testnet**
***System: Ubuntu 22.04***

### 

**Server preparation**



    sudo apt update
    sudo apt install wget curl make clang pkg-config libssl-dev build-essential -y




**Download the binary file**

```
sudo mkdir -p $HOME/avail-node && cd $HOME/avail-node
sudo wget https://github.com/availproject/avail/releases/download/v2.2.4.0-rc1/x86_64-ubuntu-2204-avail-node.tar.gz
sudo tar xvzf x86_64-ubuntu-2204-data-avail.tar.gz
sudo chmod +x data-avail
```

**Create a service**

```
yourname=<NodeName> 
```

*Ex:* **yourname=BlockSync**

## 
**Check service & logs:**


```
sudo tee /etc/systemd/system/availd.service > /dev/null << EOF
[Unit]
Description=Avail Validator
After=network.target
StartLimitIntervalSec=0
[Service]
User=$USER
Type=simple
Restart=always
RestartSec=120
ExecStart=${HOME}/avail-node/data-avail \
 -d ${HOME}/avail-node/data \
 --chain goldberg --port 30333 \
 --validator \
 --name $yourname

[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable availd
sudo systemctl restart availd && sudo journalctl -u availd -f -o cat
```

## 

**Check service & logs:**


```
sudo systemctl status availd
sudo journalctl -u availd -f -o cat
```

