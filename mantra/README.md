# install dependencies, if needed
```sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```
# install go, if needed
```cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

# set vars
```echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="mynode"" >> $HOME/.bash_profile
echo "export MANTRA_CHAIN_ID="mantra-hongbai-1"" >> $HOME/.bash_profile
echo "export MANTRA_PORT="22"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

# download binary
```upgrade_version="3.0.0"
if [ "$(uname -m)" == "aarch64" ]; then export ARCH="arm64"; else export ARCH="amd64"; fi
wget https://github.com/MANTRA-Finance/public/releases/download/v$upgrade_version/mantrachaind-$upgrade_version-linux-$ARCH.tar.gz```
# extract the binary
```tar -xvf mantrachaind-$upgrade_version-linux-$ARCH.tar.gz
chmod +x mantrachaind
mv $HOME/download/mantrachaind $HOME/go/bin
```

# config and init app
```mantrachaind config node tcp://localhost:${MANTRA_PORT}657
mantrachaind config keyring-backend os
mantrachaind config chain-id mantra-hongbai-1
mantrachaind init "mynode" --chain-id mantra-hongbai-1
```

# download genesis and addrbook
```wget -O $HOME/.mantrachain/config/genesis.json https://server-4.itrocket.net/testnet/mantra/genesis.json
wget -O $HOME/.mantrachain/config/addrbook.json  https://server-4.itrocket.net/testnet/mantra/addrbook.json
```

# set seeds and peers
```SEEDS=""
PEERS="e1b058e5cfa2b836ddaa496b10911da62dcf182e@134.65.192.193:36656,9d8acd0a6a0320ef705feb16757e14aca8b80bc0@81.17.98.206:26656,e4ab5611faceed5063357828b5e9a0ca5f26d15b@185.225.32.30:27656,30235fa097d100a14d2b534fdbf67e34e8d5f6cf@139.45.205.60:21656,33cf22311a510b01552fb1e323add74c641f01c5@65.109.62.39:18656,461895a08760159547c7b307f5a715f8f16feda8@46.166.140.185:26656,4108fe9fcb51967c16866b0b9c5bc14b4326afb0@65.108.78.101:14356,042c33fb6929d4d8a1f3d1c25694912ef1d6673e@162.55.245.144:2040,73cfc9b8f63aa9dbb923163854221db768039847@94.237.56.172:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.mantrachain/config/config.toml
```


# set custom ports in app.toml
```
sed -i.bak -e "s%:1317%:${MANTRA_PORT}317%g;
s%:8080%:${MANTRA_PORT}080%g;
s%:9090%:${MANTRA_PORT}090%g;
s%:9091%:${MANTRA_PORT}091%g;
s%:8545%:${MANTRA_PORT}545%g;
s%:8546%:${MANTRA_PORT}546%g;
s%:6065%:${MANTRA_PORT}065%g" $HOME/.mantrachain/config/app.toml
```

# set custom ports in config.toml file
```
sed -i.bak -e "s%:26658%:${MANTRA_PORT}658%g;
s%:26657%:${MANTRA_PORT}657%g;
s%:6060%:${MANTRA_PORT}060%g;
s%:26656%:${MANTRA_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${MANTRA_PORT}656\"%;
s%:26660%:${MANTRA_PORT}660%g" $HOME/.mantrachain/config/config.toml
```

# config pruning
```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.mantrachain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.mantrachain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.mantrachain/config/app.toml
```

# set minimum gas price, enable prometheus and disable indexing
```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0002uom"|g' $HOME/.mantrachain/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mantrachain/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.mantrachain/config/config.toml
```

# create service file
```sudo tee /etc/systemd/system/mantrachaind.service > /dev/null <<EOF
[Unit]
Description=Mantra node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.mantrachain
ExecStart=$(which mantrachaind) start --home $HOME/.mantrachain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

# reset and download snapshot
```mantrachaind tendermint unsafe-reset-all --home $HOME/.mantrachain
if curl -s --head curl https://server-4.itrocket.net/testnet/mantra/mantra_2024-09-20_2125218_snap.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://server-4.itrocket.net/testnet/mantra/mantra_2024-09-20_2125218_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mantrachain
    else
  echo "no snapshot founded"
fi
```

# enable and start service
```sudo systemctl daemon-reload
sudo systemctl enable mantrachaind
sudo systemctl restart mantrachaind && sudo journalctl -u mantrachaind -f
```
#Create wallet
# to create a new wallet, use the following command. don’t forget to save the mnemonic
```mantrachaind keys add $WALLET
```

# to restore exexuting wallet, use the following command
```mantrachaind keys add $WALLET --recover
```

# save wallet and validator address
```WALLET_ADDRESS=$(mantrachaind keys show $WALLET -a)
VALOPER_ADDRESS=$(mantrachaind keys show $WALLET --bech val -a)
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile
```

# check sync status, once your node is fully synced, the output from above will print "false"
```mantrachaind status 2>&1 | jq 
```

# before creating a validator, you need to fund your wallet and check balance
```mantrachaind query bank balances $WALLET_ADDRESS 
```

# Create validator
```mantrachaind tx staking create-validator \
--amount 1000000uom \
--from $WALLET \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(mantrachaind tendermint show-validator) \
--moniker "mynode" \
--identity "" \
--website "" \
--details "" \
--chain-id mantra-hongbai-1 \
--gas auto --gas-adjustment 1.5 --fees 50uom \
-y
```
