CHAIN DATA:
```
  PROJECT=Archway
  DAEMON=archwayd
  DAEMON_HOME=.archway
  CHAIN=archway-1
  GIT_REPO=https://github.com/archway-network/archway.git
  VERSION=v4.0.3
  DENOM=aarch
  MIN_GAS_PRICE=900000000000
  GENESIS=https://snapshots.polkachu.com/genesis/archway/genesis.json
  ADDRBOOK=https://snapshots.polkachu.com/addrbook/archway/addrbook.json
  SNAP_RPC=https://archway-rpc.polkachu.com:443
```



<details>
  <summary>
 <b>Prepare a server</b>
  </summary>
  
#### Update System Packages
```bash
sudo apt update && sudo apt upgrade -y
```

#### Install Required Packages
```bash
sudo apt install curl tar wget clang tree pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y
```

<details>
  <summary>
 Package Descriptions
  </summary>
      
>  - curl, wget: Tools for downloading files.  
> - clang, build-essential: Necessary for compiling software.  
> - pkg-config, libssl-dev, libleveldb-dev: Development libraries for software building.  
> - jq: Command-line JSON processor.  
> - git: Version control system.  
> - make, bsdmainutils: Build tools and utilities.  
> - ncdu, htop, screen: System monitoring and management utilities.  
> - unzip, bc: Tools for file decompression and arithmetic operations.  
> - fail2ban: Security utility to prevent brute force attacks.  

</details>

#### Install Go v1.21.6
```bash
ver="1.21.6" && \
cd $HOME && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
```
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/utils/main/installers/golang.sh)
```

</details>

### Node Install

#### Installing Node Binary Files


  <details>
  <summary>
 Option 1: Building from Source
  </summary>
    
```bash
git clone https://github.com/archway-network/archway.git archway && \
cd archway && \
git checkout v4.0.3 && \
make install
```
```bash
archwayd version --long | grep -e version -e commit
```
> version: v4.0.3       
commit: 3cc9228982f651d3a54b395d6ff026e61e91f4b6



</details>


  <details>
  <summary>
 Option 2: Downloading the Precompiled Binarye
  </summary>
    
```bash
cd $HOME
rm -rf archway
wget -O archwayd https://github.com/archway-network/archway/releases/download/v4.0.3/archwayd_linux_amd64
chmod +x archwayd
mv archwayd go/bin
```
```bash
archwayd version --long | grep -e version -e commit
```
> version: v4.0.3       
commit: 3cc9228982f651d3a54b395d6ff026e61e91f4b6
    
</details>


##### Initializing the Archway Node and Configuring Settings
```bash
archwayd init ENZORO_GUIDE --chain-id archway-1 && \
archwayd config chain-id archway-1 && \
archwayd config keyring-backend os
```

  <details>
  <summary>
 Cosmovisor
  </summary>

```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```
cosmovisor version --cosmovisor-only
```
> cosmovisor version: v1.5.0
#### Create Cosmovisor Folders
```
mkdir -p ~/.archway/cosmovisor/genesis/bin
mkdir -p ~/.archway/cosmovisor/upgrades
```
#### Load Node Binary into Cosmovisor Folder
```
cp ~/go/bin/archwayd ~/.archway/cosmovisor/genesis/bin
```

</details>

```
NODES_NUM="2"

EXTERNAL_IP=$(wget -qO- eth0.me)

sed -i.bak -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((NODES_NUM + 266))58\"/" \
            -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((NODES_NUM + 266))57\"/" \
            -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$((NODES_NUM + 60))60\"/" \
            -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$((NODES_NUM + 266))56\"/}" \
            -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$((NODES_NUM + 266))56\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$((NODES_NUM + 266))56\"/}" \
            -e "s/\(prometheus_listen_addr = \":\)\([0-9]*\).*/\1$((NODES_NUM + 266))60\"/" $HOME/.archway/config/config.toml

sed -i.bak \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((NODES_NUM + 13))17\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((NODES_NUM + 90))90\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((NODES_NUM + 90))91\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((NODES_NUM + 85))45\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(ws-address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$((NODES_NUM + 85))46\4/}"  $HOME/.archway/config/app.toml 
echo "export NODE=http://localhost:$((NODES_NUM+266))57" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
archwayd config node $NODE
```
<details>
  <summary>
Pruning_relayer
  </summary>
  
```
indexer="kv" && \
snapshot_interval="2000" && \
pruning="custom" && \
pruning_keep_recent="40000" && \
pruning_keep_every="2000" && \
pruning_interval="17" && \
api_enable="false" && \
grpc_enable="true" && \
grpc_web_enable="false" && \
json_rpc_enable="false" && \
filter_peers="true" && \
prometheus="false" && \
rpc_address="0.0.0.0" && \
minimum_gas_prices="0aarch"
```
</details>

<details>
  <summary>
Pruning_test_relayer
  </summary>
  
```

indexer="kv" && \
snapshot_interval="2000" && \
pruning="custom" && \
pruning_keep_recent="20000" && \
pruning_keep_every="2000" && \
pruning_interval="17" && \
api_enable="true" && \
grpc_enable="true" && \
grpc_web_enable="true" && \
json_rpc_enable="false" && \
filter_peers="true" && \
prometheus="false" && \
rpc_address="127.0.0.1" && \
minimum_gas_prices="0$TOKEN" 
```
</details>

<details>
  <summary>
Для валидатора
  </summary>
  
```
indexer="null" && \
snapshot_interval="100" && \
pruning="custom" && \
pruning_keep_recent="100" && \
pruning_keep_every="0" && \
pruning_interval="17" && \
api_enable="false" && \
grpc_enable="false" && \
grpc_web_enable="false" && \
json_rpc_enable="false" && \
filter_peers="true" && \
prometheus="false" && \
rpc_address="127.0.0.1" && \
minimum_gas_prices="0.001$TOKEN"
```

</details>

```
sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.archway/config/config.toml && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/"  $HOME/.archway/config/app.toml && \
sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/"  $HOME/.archway/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/"  $HOME/.archway/config/app.toml && \
sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/"  $HOME/.archway/config/app.toml && \
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/"  $HOME/.archway/config/app.toml && \
sed -i.bak -e "/^\[api\]/,/^enable/s/^enable *=.*/enable = '$api_enable'/"  $HOME/.archway/config/app.toml && \
sed -i.bak -e "/^\[grpc\]/,/^enable/s/^enable *=.*/enable = '$grpc_enable'/" $HOME/.archway/config/app.toml && \
sed -i.bak -e "/^\[grpc-web\]/,/^enable/s/^enable *=.*/enable = '$grpc_web_enable'/"  $HOME/.archway/config/app.toml && \
sed -i.bak "/^\[rpc\]/,/^laddr =/s/laddr = \"tcp:\/\/127\.0\.0\.1:/laddr = \"tcp:\/\/$rpc_address:/"  $HOME/.archway/config/config.toml && \
sed -i.bak -e 's/^filter_peers *=.*/filter_peers = '$filter_peers'/' $HOME/.archway/config/config.toml && \
sed -i.bak -e "/^\[instrumentation\]/,/^prometheus/s/^prometheus *=.*/prometheus = '$prometheus'/"  $HOME/.archway/config/config.toml && \
sed -i.bak -e 's/^minimum-gas-prices *=.*/minimum-gas-prices = "'$minimum_gas_prices'"/'  $HOME/.archway/config/app.toml  && \
sed -i.bak -e "/^\[json-rpc\]/,/^enable/s/^enable *=.*/enable = '$json_rpc_enable'/"  $HOME/.archway/config/app.toml
```
##### Downloading Genesis and Address Book Files
```bash
wget -O $HOME/.archway/config/genesis.json https://snapshots.polkachu.com/genesis/archway/genesis.json
```
> sha256sum ~/.sidechain/config/genesis.json   
> 69bb55baeed046704d09f7590d8969dfafcc5f6afbd00d9479bd17ced44b7708

```bash
wget -O $HOME/.archway/config/addrbook.json https://snapshots.polkachu.com/addrbook/archway/addrbook.json
```

```
sudo tee /etc/systemd/system/$USER.service > /dev/null <<EOF
[Unit]
Description="Archway node"
After=network-online.target

[Service]
User=$USER
Environment="DAEMON_NAME=archwayd"
Environment="DAEMON_HOME=$HOME/.archway"
Environment="UNSAFE_SKIP_BACKUP=true"
ExecStart=`which cosmovisor` run start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
