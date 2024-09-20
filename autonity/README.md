#**Install Aut and Create Wallet.Key**
```
sudo apt update && apt upgrade -y

sudo apt install git curl wget -y

sudo apt install make clang pkg-config libssl-dev build-essential -y

sudo apt install pipx -y

pipx install --force git+https://github.com/autonity/aut.git

pipx ensurepath

source ~/.bashrc
```
#**<TREASURY_ADDRESS>**
```
mkdir piccadilly-keystore && aut account new --keyfile ./piccadilly-keystore/wallet.key
```
# Create a config file holding the rpc endpoint.
```
echo '[aut]' > .autrc
echo 'rpc_endpoint = https://rpc1.piccadilly.autonity.org/' >> .autrc
echo 'keyfile = /root/piccadilly-keystore/wallet.key' >> .autrc
```
**#Create Oracle Key
#<ORACLE_ADDRESS>**
```
aut account new --keyfile ./piccadilly-keystore/oracle.key
```
**#Running a Node**
```
mkdir autonity-go-client
cd autonity-go-client
wget https://github.com/autonity/autonity/releases/download/v0.14.0/autonity-linux-amd64-0.14.0.tar.gz
tar zxvf autonity-linux-amd64-0.14.0.tar.gz && rm autonity-linux-amd64-0.14.0.tar.gz
sudo cp -r autonity /usr/local/bin/autonity
````
**#Run Autonity**
```
cd && mkdir autonity-chaindata
```
```
sudo tee /etc/systemd/system/autonityd.service > /dev/null << EOF
[Unit]
Description=Autonityd Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=autonity \
    --datadir /root/autonity-chaindata \
    --piccadilly  \
    --http  \
    --http.addr 0.0.0.0 \
    --http.api aut,eth,net,txpool,web3,admin \
    --http.vhosts "*" \
    --ws  \
    --ws.addr 0.0.0.0 \
    --ws.api aut,eth,net,txpool,web3,admin  \
    --nat extip:176.9.48.16 \
    ;
[Install]
WantedBy=multi-user.target
EOF
```
```
systemctl enable autonityd && systemctl restart autonityd && journalctl -fu autonityd -o cat
```

**#Install Autonity Oracle Server**
```
cd && wget https://github.com/autonity/autonity-oracle/releases/download/v0.1.9/autonity-oracle.tgz && tar zxvf autonity-oracle.tgz && cd autonity-oracle && sudo cp -r autoracle /usr/local/bin/autoracle
```
```
sudo tee /etc/systemd/system/autoracled.service > /dev/null << EOF
[Unit]
Description=Autoracled Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=autoracle \
    -key.file="${HOME}/piccadilly-keystore/oracle.key" \
    -key.password="cze69qzm" \
    -ws="ws://127.0.0.1:8546" \
    -plugin.conf="${HOME}/autonity-oracle/plugins-conf.yml" \
    -plugin.dir="${HOME}/autonity-oracle/plugins/" \
  
[Install]
WantedBy=multi-user.target
EOF
```
```
systemctl enable autoracled && systemctl start autoracled && journalctl -fu autoracled -o cat
```
#Send fun to oracle address
```
aut tx make --to <oracle address> --value 0.1 | aut tx sign - | aut tx send -
```
```
cd ~/autonity-go-client/
wget https://block-sync.com/services/autonity//ethkey
chmod +x ethkey
./ethkey inspect --private /root/piccadilly-keystore/oracle.key
```
**#copy Private key**
```
echo '<Private key>' > /root/piccadilly-keystore/oraclehex.key
```
**#Generate a cryptographic proof of node ownership <PROOF>**
```
autonity genOwnershipProof --autonitykeys /root/autonity-chaindata/autonity/autonitykeys --oraclekey /root/piccadilly-keystore/oraclehex.key <TREASURY_ACCOUNT_ADDRESS>
```
#waiting sync complete and Connecting to your Node by Configure aut rpc_endpoint=http://XX.XX.XX.XX:8545/ in .autrc file
```
cd && nano .autrc
#replace rpc_endpoint = https://rpc1.piccadilly.autonity.org/ by rpc_endpoint = http://0.0.0.0:8545/
```
```
aut node info
```
#Result: <ENODE_URL>
"admin_enode": "enode://cef6334d0855b72dadaa923ceae532550exxxx0288a393eda5d811b9e81053e1324e637a202e21d04e301fe1765900bdd9f3873d58a2badf693331cb1b15@751.11.121.34:30303"


**#The validator address**
```
aut validator compute-address enode://cef6334d0855b72dadaa923ceae532550exxxx0288a393eda5d811b9e81053e1324e637a202e21d04e301fe1765900bdd9f3873d58a2badf693331cb1b15@751.11.121.34:30303
```
**#Determine the validator consensus public key**
```
cd ~/autonity-go-client/ && ./ethkey autinspect /root/autonity-chaindata/autonity/autonitykeys
```
#result: <CONSENSUS_PUBLIC_KEY>
```
Node Address:           0x550454xxxxx8e1EAD5FxxxxxF59439B18E249
Node Public Key:        0xcef6334dxxx72dadaa923ceae532550ef68e0acxxx88a393eda5d811b9e81053e1324e637axxx4e301fe1765900bdd9f3873d58a2badf693331cb1b15
Consensus Public Key:   0x1aa83a28e2xxx41fg01ccc46e2b8d9dc16df3b6ff87ffa5ff6d7fxxx9a60563237cd66a256f60a92e71
```

**#Verifying your ownership proof against your keys**
```
./ethkey verifypop <TREASURY_ADDRESS> <ENODE_URL> <ORACLE_ADDRESS> <CONSENSUS_PUBLIC_KEY> <PROOF>
```
**#Submit the registration transaction **
```
aut validator register <ENODE> <ORACLE ADDRESS> <CONSENSUS_KEY> <PROOF> | aut tx sign - | aut tx send -
```
```
aut validator list
```
```
head -c 64 /root/autonity-chaindata/autonity/autonitykeys
```
```
echo '89f7e8beaae33a2ddexxxxxxd3917822534xxxxxx2e947d5fba151' > /root/piccadilly-keystore/autonitykey
```
```
aut account import-private-key /root/piccadilly-keystore/autonitykey
```
```
aut account sign-message 'Application for the stake delegation program'
```
