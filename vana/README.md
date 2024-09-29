## Vana DLP Setup Guide
### Setting Up Your Environment
This guide will walk you through setting up the necessary tools and environment for either creating a DLP (Data Liquidity Pool) or joining one as a validator.
## Requirements
For all participants:
- Git
- Python 3.11+
- Poetry
- Metamask or any EVM-compatible wallet

## Additional requirements for DLP creators:
- Node.js and npm
### Step 1: Install Python 3.11
Start by ensuring your Python version is 3.11 or higher. Install it using the following steps:
```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.11 python3.11-venv python3.11-dev
python3.11 --version
```
### Step 2: Install Poetry
Poetry is required for managing project dependencies. To install:
```bash
curl -sSL https://install.python-poetry.org | python3 -
echo 'export PATH="$HOME/.local/bin:$PATH"' >> $HOME/.bash_profile
source $HOME/.bash_profile
poetry --version
```
### Step 3: Install Node.js & npm
To manage Node.js versions, install **nvm** (Node Version Manager):
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
echo 'export NVM_DIR="$HOME/.nvm"' >> $HOME/.bash_profile
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> $HOME/.bash_profile
echo '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"' >> $HOME/.bash_profile
source $HOME/.bash_profile
nvm install --lts
```
Verify installation:
```bash
node -v
npm -v
```
## Initial Setup
### Step 1: Clone the Vana DLP Git Repository
First, get a copy of the Vana DLP ChatGPT repository:
```bash
git clone https://github.com/vana-com/vana-dlp-chatgpt.git
cd vana-dlp-chatgpt
```
### Step 2: Install Dependencies
Next, install the necessary dependencies using Poetry:
```bash
poetry install
```
### Step 3: Install Vana CLI
To interact with Vana, install the command-line tool:
```bash
pip install vana
```
### Step 4: Create Wallet
Generate your wallet for DLP participation:
```bash
vanacli wallet create --wallet.name default --wallet.hotkey default
```
This will generate two key pairs:
- **Coldkey**: for manually managed actions (like staking).
- **Hotkey**: for validator-related operations (like submitting scores).
Add **Satori Testnet** to Metamask:
- **Network name**: Satori Testnet
- **RPC URL**: `https://rpc.satori.vana.org`
- **Chain ID**: `14801`
- **Currency**: VANA
### Step 5: Export Private Keys
Secure your keys by exporting them:
```bash
# Coldkey
vanacli wallet export_private_key
# Hotkey
vanacli wallet export_private_key
```
Import these keys into Metamask.
### Step 6: Fund Wallet
Use the [VANA faucet](https://faucet.vana.org) to request testnet tokens for both your coldkey and hotkey addresses.
Creating a DLP
Generate Encryption Keys
Generate RSA key pairs for file encryption and decryption:
```bash
./keygen.sh
```
This will prompt you for personal details and generate key files.
Deploy Smart Contracts
First, clone the DLP smart contract repository:
```bash
cd $HOME
git clone https://github.com/vana-com/vana-dlp-smart-contracts.git
cd vana-dlp-smart-contracts
```
Install **Yarn** and the contract dependencies:
```bash
npm install -g yarn
yarn install
```
Update the `.env` file with your information, then deploy the contracts:
```bash
cp .env.example .env
nano .env
npx hardhat deploy --network satori --tags DLPDeploy
```
Contract Verification
Verify the deployed contracts:
```bash
npx hardhat verify --network satori <DataLiquidityPool address>
npx hardhat verify --network satori <DataLiquidityPoolToken address> "<DLP_TOKEN_NAME>" <DLP_TOKEN_SYMBOL> <OWNER_ADDRESS>
```
## Configure the DLP Contract
Go to your contract's proxy page on the Satori explorer and perform the following actions:
### 1. Set `updateFileRewardDelay` to `0`.
### 2. Call `addRewardsForContributors` with `1000000000000000000000000` (1 million tokens).
## Validator Setup
### Step 1: Fund Your Validator
If you're the DLP creator, send your DLP tokens to your coldkey and hotkey addresses. If you're joining, request tokens from the DLP creator.
### Step 2: Register Validator
Run the following commands to register your validator:
```bash
./vanacli dlp register_validator --stake_amount 10
./vanacli dlp approve_validator --validator_address=<your hotkey address>
```
### Step 3: Run the Validator
To start the validator process:
```bash
poetry run python -m chatgpt.nodes.validator
```
### For continuous background execution, use **systemd**:
```bash
echo $(which poetry)
sudo tee /etc/systemd/system/vana.service << EOF
[Unit]
Description=Vana Validator Service
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/vana-dlp-chatgpt
ExecStart=/root/.local/bin/poetry run python -m chatgpt.nodes.validator
Restart=on-failure
RestartSec=10
Environment=PATH=/root/.local/bin:/usr/local/bin:/usr/bin:/bin:/root/vana-dlp-chatgpt/myenv/bin
Environment=PYTHONPATH=/root/vana-dlp-chatgpt

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable vana.service
sudo systemctl start vana.service
sudo systemctl status vana.service
```
### Check logs:
```bash
sudo journalctl -u vana.service -f
```
