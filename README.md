# Aptos-Incentivized-Testnet-Guide
If you have run a devnet full node before, please stop it first to prevent port conflicts from running the testnet validator node.

Install Docker and docker-compose
```
# Install Docker
wget -O get-docker.sh https://get.docker.com 
sudo sh get-docker.sh
rm -f get-docker.sh

# Install docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
Create Directory

```
sudo mkdir -p ~/aptos-node/testnet && cd ~/aptos-node/testnet
```

Generate private keys
```
sudo docker run --rm \
  -v $(pwd):/data/aptos-cli \
  jiangydev/aptos-cli:v0.1.1 \
  aptos genesis generate-keys --output-dir /data/aptos-cli
```
Download files
```
sudo wget -O docker-compose.yaml https://raw.githubusercontent.com/aptos-labs/aptos-core/main/docker/compose/aptos-node/docker-compose.yaml
sudo wget -O validator.yaml https://raw.githubusercontent.com/aptos-labs/aptos-core/main/docker/compose/aptos-node/validator.yaml
sudo wget -O fullnode.yaml https://raw.githubusercontent.com/aptos-labs/aptos-core/main/docker/compose/aptos-node/fullnode.yaml

```

Generate Validator Info
```
sudo docker run --rm \
  -v $(pwd):/data/aptos-cli \
  jiangydev/aptos-cli:v0.1.1 \
  aptos genesis set-validator-configuration \
  --keys-dir /data/aptos-cli --local-repository-dir /data/aptos-cli \
  --username <your-node-name> \
  --validator-host <your-vlidator-node-ip:port> \
  --full-node-host <your-full-node-ip:port>
  ```
  Example:
  ```
  sudo docker run --rm \
  -v $(pwd):/data/aptos-cli \
  jiangydev/aptos-cli:v0.1.1 \
  aptos genesis set-validator-configuration \
  --keys-dir /data/aptos-cli --local-repository-dir /data/aptos-cli \
  --username Aptosnode \
  --validator-host 49.12.236.115:6180 \
  --full-node-host 49.12.236.115:6182
  ```
  
  Generate layout YAML file
  ```
  sudo bash -c "cat > layout.yaml" <<EOF
---
root_key: "0x5243ca72b0766d9e9cbf2debf6153443b01a1e0e6d086c7ea206eaf6f8043956"
users:
  - <your-node-name>
chain_id: 23
  ```
  Example
 ```
 sudo bash -c "cat > layout.yaml" <<EOF
---
root_key: "0x5243ca72b0766d9e9cbf2debf6153443b01a1e0e6d086c7ea206eaf6f8043956"
users:
  - MoveMove
chain_id: 23
EOF
```

Generate genesis blob  and waypoint file
```
sudo docker run --rm \
  -v $(pwd):/data/aptos-cli \
  jiangydev/aptos-cli:v0.1.1 \
  sh -c "rm -rf /data/aptos-cli/genesis.blob && rm -rf /data/aptos-cli/waypoint.txt && rm -rf /data/aptos-cli/framework && cp -r /framework /data/aptos-cli && aptos genesis generate-genesis --local-repository-dir /data/aptos-cli --output-dir /data/aptos-cli && rm -rf /data/aptos-cli/framework"
  ```
  Run node
  ```
  sudo docker-compose up -d
  ```

*You can run node by one scrip (from https://nodes.guru/)
```
wget -q -O aptos.sh https://api.nodes.guru/aptos.sh && chmod +x aptos.sh && sudo /bin/bash aptos.sh
```




