Sonaric is an AI-powered network that revolutionizes the way blockchain nodes are operated and managed. Unlike traditional DePINs or compute marketplaces, Sonaric enables node operators to run blockchain nodes on their own hardware, driven by their own interests and incentives.

By combining intelligent automation with a decentralized network, Sonaric makes it easy for anyone to become a node operator, while providing blockchain networks with a reliable and optimized pool of compute resources to support their growth. The network's AI-driven approach ensures optimal performance, security, and rewards for node operators, without the need for complex proof of computation mechanisms. 

Twitter: https://x.com/Sonaricnetwork

Discord: https://discord.gg/MZ247hw47z

Explorer: https://tracker.sonaric.xyz/

Sonaric Incentives: https://docs.sonaric.xyz/community/incentive-program.html

Guide dev: https://docs.sonaric.xyz/installation/vps.html

Server preparation
sudo apt update && apt upgrade -y
sudo apt install wget curl make clang net-tools pkg-config libssl-dev build-essential jq lz4 gcc unzip snapd -y
Install Node
sh -c "$(curl -fsSL https://raw.githubusercontent.com/monk-io/sonaric-install/main/linux-install-sonaric.sh)"
Check node info
sonaric node-info
Check points
sonaric points
Backup Wallet (all in one file) - Pub ID - Priv Key - Seed
sonaric identity-export -o wallet.identity
You will be prompted to enter a password to encrypt the exported identity

Important: Save wallet.identity file on your server and copied it to your own computer

Recovery - You can then import it on a new node using
sonaric identity-import -i wallet.identity
Rename node
sonaric node-rename
Upgrade node
sudo apt update
sudo apt upgrade sonaric
sonaric node-info
