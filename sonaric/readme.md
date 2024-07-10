
*Sonaric is an AI-powered network that revolutionizes the way blockchain nodes are operated and managed. Unlike traditional DePINs or compute marketplaces, Sonaric enables node operators to run blockchain nodes on their own hardware, driven by their own interests and incentives.
By combining intelligent automation with a decentralized network, Sonaric makes it easy for anyone to become a node operator, while providing blockchain networks with a reliable and optimized pool of compute resources to support their growth. The network's AI-driven approach ensures optimal performance, security, and rewards for node operators, without the need for complex proof of computation mechanisms.* 
![enter image description here](https://github.com/tiendat247/testnet/blob/main/sonaric/sonaric.jpg)

Twitter: https://x.com/Sonaricnetwork

Discord: https://discord.gg/MZ247hw47z

Explorer: https://tracker.sonaric.xyz/

Sonaric Incentives: https://docs.sonaric.xyz/community/incentive-program.html

Guide dev: https://docs.sonaric.xyz/installation/vps.html

**#Server preparation**

    sudo apt update && sudo apt upgrade -y
    sudo apt install git wget curl make clang pkg-config libssl-dev build-essential lz4 snapd protobuf-compiler jq gcc curl -y

**#Installation**

     wget https://raw.githubusercontent.com/monk-io/sonaric-install/main/linux-install-sonaric.sh
     bash linux-install-sonaric.sh

**#Check Node information**

    sonaric node-info

**#Check Node Points**

    sonaric points

**#Backup Wallet**

    sonaric identity-export -o wallet.identity
**<<IMPORTANT!!>>**
Enter password and a "wallet.identity" file will be created at root folder. Download and save this file to safety place 

**#Recover Wallet**

    sonaric identity-import -i wallet.identity

**#Re-name your node**

    sonaric node-rename

**#Node upgrading**

    sudo apt upgrade sonaric

