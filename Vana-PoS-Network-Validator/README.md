# Vana PoS Network Validator Setup

## This guide will walk you through setting up a validator node for the Vana Proof-of-Stake (PoS) network using Docker.
> **⚠️ Warning:** Please run the validator only after conducting your own research and at your own risk. To activate the validator, you may need to be whitelisted, which requires staking 35,000 $VANA.
## Prerequisites
### Install Docker & Docker Compose

Run the following commands to install Docker on your Ubuntu system:
```
sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker && sudo systemctl enable docker
sudo docker --version
```
### Install Docker Compose
Run this command to install Docker Compose:
```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```
## Testnet Requirements
### Minimum Hardware Specifications:
- **CPU**: 2-core
- **RAM**: 8 GB
- **Storage**: 100 GB high-speed SSD
- **Architecture**: x86-64
  
Ensure that your machine meets the above requirements for optimal performance on the testnet.

### Quick Start
**1. Clone the Vana Repository:**
```
git clone https://github.com/vana-com/vana.git
cd vana
```
**2. Configure the Environment**

Copy the example environment file and modify it with your preferred configuration:
```
cp .env.example .env
```
- Open the `.env` file using a text editor:
```
nano .env
```
- Set the `EXTERNAL_IP`=`{Your-VPS-IP}`
- Set `USE_VALIDATOR=true` to run a validator node.
- Set the appropriate `DEPOSIT_*` variables (e.g., RPC URL, contract address, and private key for deposits).

**3. Generate Validator Keys**

This will guide you through an interactive process to generate the keys needed for your validator:
```
docker compose --profile manual run --rm validator-keygen
```
> **⚠️ Important:** Carefully read the instructions and make sure to save your seed phrase/mnemonic for the validator wallet in a secure location!

**4. Submit Validator Deposits**

After generating your validator keys, you need to submit the deposit for each validator to stake:
```
docker compose --profile init --profile manual run --rm submit-deposits
```
Wait for the transactions to be confirmed before proceeding.

**5. Start All Services**

Once your deposit is confirmed, start all the necessary services (including the validator):
```
docker compose --profile init --profile validator up -d
```
**6. Check Logs**

Monitor your logs to ensure everything is running smoothly:
```
docker ps
```
```
docker logs --tail 50 -f {container-id}
```
**7. Troubleshooting**

- If the `check-config` service fails, check its logs with:
```
docker compose logs check-config
```
- Ensure all configuration files are correctly formatted, and ports are open in your firewall.

**8. Security Considerations**
- Securely store your validator keys and **never** share them.
- Regularly update your node software and monitor your validator's performance.

