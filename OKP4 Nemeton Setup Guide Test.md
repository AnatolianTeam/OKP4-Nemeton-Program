# OKP4 Nemeton Node Setup Guide


## Links:   
[Explorer](https://okp4.explorers.guru) | [Explorer](https://test.anode.team/opk4) 

[Faucet](https://faucet.okp4.network/)  

[Discord](https://discord.com/invite/okp4) | [Twitter](https://twitter.com/OKP4_Protocol) | [Telegram](https://t.me/okp4network)

[Github](https://github.com/okp4)  | [Website](https://okp4.network/)  | [Medium](https://blog.okp4.network/) | [Linkedin](https://www.linkedin.com/company/okp4-open-knowledge-platform-for) | [Whitepaper](https://docs.okp4.network/whitepaper/abstract%20)


## Guide navigation:
* [Prepare](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#prepare)
* [All variables](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#all-variables)
* [Build and configuration](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#build-and-configuration)
* [Change PORT](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#change-port)
* [Memory optimization](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#memory-optimization)
* [Start node](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#start-node)
* [Create a validator](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#create-a-validator)
* [Snapshot](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#snapshot)
* [Update node](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#update-node)
* [Useful commands](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#useful-commands)
* [Delete node](https://github.com/romanv1812/OKP4/blob/main/Sidh/Setup%20your%20node.md#delete-node)

## Getting Root Authority and Switching to Root Directory
```bash
sudo su
cd /root
```
## System Update and Install Packages
```bash
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev libleveldb-dev jq build-essential bsdmainutils git make ncdu htop screen unzip bc fail2ban htop -y
```

## Installing GO
```bash
cd $HOME && \
ver="1.19.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## Adding Variables
* The places you need to change are listed below.
  - `$OKP4_NODENAME` validator name
  - `$OKP4_WALLET` your OKP4 wallet name
  - `$OKP4_WALLET_PASS` your wallet password
```bash
echo "export OKP4_NODENAME=$OKP4_NODENAME"  >> $HOME/.bash_profile
echo "export OKP4_WALLET=$OKP4_WALLET" >> $HOME/.bash_profile
echo "export OKP4_CHAIN_ID=okp4-nemeton-1" >> $HOME/.bash_profile
echo "export OKP4_PORT=11" >> $HOME/.bash_profile
echo "export OKP4_WALLET_PASS=$OKP4_WALLET_PASS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Downloading and Installing Sei Files
```bash 
git clone https://github.com/okp4/okp4d.git okp4d && \
cd okp4d && \
make install
```

## Configuring and Launching the Application
```bash 
okp4d config keyring-backend test  && \
okp4d config chain-id $OKP4_CHAIN_ID && \
okp4d init $OKP4_NODENAME --chain-id $OKP4_CHAIN_ID
```

## Downloading Genesis File
```bash 
wget -O $HOME/.okp4d/config/genesis.json https://raw.githubusercontent.com/okp4/networks/main/chains/nemeton-1/genesis.json
```

## Setting the Minimum GAS Fee
```bash 
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uknow\"/" $HOME/.okp4d/config/app.toml
```

## Setting SEED and PEERS
```bash 
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"9c462b1c0ba63115bd70c3bd4f2935fcb93721d0@65.21.170.3:42656,ee4c5d9a8ac7401f996ef9c4d79b8abda9505400@144.76.97.251:12656,2e85c1d08cfca6982c74ef2b67251aa459dd9b2f@65.109.85.170:43656,264256d32511c512a0a9d4098310a057c9999fd1@okp4.sergo.dev:12233,4ea26ce893d8f4f89a7b49b9bd77e0fbd914e029@65.109.88.162:36656,8d8fdad759361a57121903632adbd66ad072b1ab@okp4-testnet.nodejumper.io:29656,e3c602b146121c88d350bd7e0f6ce8977e1aacff@161.97.122.216:26656,3c805c2dead7b7a3a1d3ba2399d4d62153322413@65.108.2.41:36656,9d1482bc31fb4578a5c7f7f65c4e0aaf2dfc2336@213.239.215.77:34656,a7f1dcf7441761b0e0e1f8c6fdc79d3904c22c01@[2a02:c206:2093:4875::1]:36656,a7f1dcf7441761b0e0e1f8c6fdc79d3904c22c01@38.242.150.63:36656,99f6675049e22a0216af0e2447e7a4c5021874cd@142.132.132.200:28656,9392c27a9a561c31e7a920dc6f577d663c473ef8@154.12.225.88:26656\"/; s/^seeds *=.*/seeds = \"$SEEDS\"/" $HOME/.okp4d/config/config.toml
```

## Setting Pruning
```bash
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.Cardchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.okp4d/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.okp4d/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.okp4d/config/app.toml
```

## Activating Prometheus
```bash
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.okp4d/config/config.toml
```

## Closing Indexer
```bash
sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.okp4d/config/config.toml
```

## Setting Ports
```shell
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${OKP4_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${OKP4_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${OKP4_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${OKP4_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${OKP4_PORT}660\"%" $HOME/.okp4d/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${OKP4_PORT}317\"%; s%^address = \":8080\"%address = \":${OKP4_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${OKP4_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${OKP4_PORT}091\"%" $HOME/.okp4d/config/app.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${OKP4_PORT}657\"%" $HOME/.okp4d/config/client.toml
```

## Creating Service File
```bash
tee <<EOF >/dev/null /etc/systemd/system/okp4d.service
[Unit]
Description=OKP4 Nemeton Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which okp4d) start
Restart=always
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Starting Service
```bash
systemctl daemon-reload && \
systemctl enable okp4d && \
systemctl restart okp4d
```

## Checking Logs
```bash
journalctl -u okp4d -f -o cat
```

## Creating a Wallet
### Creating a New Wallet
We do not change the `$OKP4_WALLET` part, we set a name for our wallet at the beginning of the installation.
```bash
okp4d keys add $OKP4_WALLET --keyring-backend file
```

### Importing Existing Wallet
```bash
okp4d keys add $OKP4_WALLET --recover
```

## Wallet and Valoper Information
Here we add our wallet and valoper information to the variable.

```bash
OKP4_WALLET_ADDRESS=$(okp4d keys show $OKP4_WALLET -a)
$OKP4_WALLET_PASS
OKP4_VALOPER_ADDRESS=$(okp4d keys show $OKP4_WALLET --bech val -a)
```

```bash
echo 'export OKP4_WALLET_ADDRESS='${OKP4_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export OKP4_VALOPER_ADDRESS='${OKP4_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

🔴 **If you get an error like below while creating or importing a wallet**
 * `-bash: /root/go/bin/okp4d: No such file or directory`
Enter this code;
```bash
cp /usr/bin/okp4d /root/go/bin
```

## Checking Sync
As long as we get `false` output, we do not move on to the next step, namely creating a validator.
```bash
okp4d status 2>&1 | jq .SyncInfo
```

echo "export OKP4_WALLET=$OKP4_WALLET" >> $HOME/.bash_profile
echo "export OKP4_CHAIN_ID=okp4-nemeton-1" >> $HOME/.bash_profile
echo "export OKP4_PORT=11" >> $HOME/.bash_profile
echo "export OKP4_WALLET_PASS=$OKP4_WALLET_PASS" >> $HOME/.bash_profile


## Creating a Validator
  You do not need to make any changes in the command below, except for the places I have specified below;
    - `identity`: where it says `XXXX1111XXXX1111`, you write your identification number given to you as a member of [keybase](https://keybase.io/) site.
    - `details`: You can write information about yourself where it says `Always forward with the Anatolian Team 🚀`.
    - `website`: `https://anatolianteam.com` if you have a site or twitter etc. You can write your address.
    - `security-contact`: Your email address.

```bash 
okp4d tx staking create-validator \
--amount=1000000uknow \
--pubkey=$(okp4d tendermint show-validator) \
--moniker=OKP4_NODENAME \
--chain-id=$OKP4_CHAIN_ID \
--commission-rate="0.1" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.1" \
--min-self-delegation="1" \
--from=$OKP4_WALLET \
--details="Always forward with the Anatolian Team 🚀" \
--security-contact="info@anatolianteam.com" \
--website="https://anatolianteam.com" \
--identity="D06CA33098EDDACE" \
--yes
``` 

## Create a validator
```bash 
okp4d tx staking create-validator \
  --amount=1000000uknow \
  --pubkey=$(okp4d tendermint show-validator) \
  --moniker=$MONIKER \
  --chain-id=okp4-nemeton-1 \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation=1000000 \
  --fees=0uknow \
  --from=$WALLET \
  --identity=$IDENTITY \
  --website=$WEBSITE \
  --details=$DETAILS \
  --security-contact=$SECURITY_CONTACT \
  -y
  ```
## Snapshot
If you did not find an open RPC, you can use the [parser](https://github.com/romanv1812/OKP4/blob/main/rpc_parser.sh) to search in the addrbook.
<img width="1078" alt="image" src="https://user-images.githubusercontent.com/83868103/210120043-e662ea3d-5c2e-4d06-9f62-162c6005ec70.png">

```bash
sudo systemctl stop okp4d && \
okp4d tendermint unsafe-reset-all --home $HOME/.okp4d --keep-addr-book
```
```bash
# RPC example: 5.161.106.127:26657
SNAP_RPC=""
```
```bash
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT-100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```
```bash
# Check data
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
# Output example (numbers will be different):
# 376080 374080 F0C78FD4AE4DB5E76A298206AE3C602FF30668C521D753BB7C435771AEA47189
```
```bash
# Peer example: 22cd56c20132817d609025f42c5e263e70157e64@5.161.106.127:26656
peers=""
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.okp4d/config/config.toml
```
```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.okp4d/config/config.toml
```
```bash
sudo systemctl restart okp4d && sudo journalctl -u okp4d -f -o cat
```
## Updating node
```bash
TAG_NAME=""
```
```bash
sudo systemctl stop okp4d && \
cd okp4d && \
git pull; \
git checkout tags/$TAG_NAME && \
make clean; \
make install && \
sudo systemctl restart okp4d && \
journalctl -u okp4d -f -o cat
```

## USEFUL COMMANDS

### Node status
```bash
# Service logs
journalctl -u okp4d -f -o cat
```
```bash
# Service status
systemctl status okp4d
```
```bash
# Check node status
curl -s $NODE/status
```
```bash
# Check synchronization of your node, if the result is false, the node is synchronized
curl -s $NODE/status | jq .result.sync_info.catching_up
```
```bash
# Check consensus 
curl -s $NODE/consensus_state  | jq '.result.round_state.height_vote_set[0].prevotes_bit_array'
```
```bash
# Connected peers
curl -s $NODE/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```

### Validator info
```bash
# Get validator address 
echo $VALOPER
```
```bash
# Get wallet address
echo $ADDRESS
```
```bash
# Jail, tombstoned, start_height, index_offset
okp4d q slashing signing-info $(okp4d tendermint show-validator)
```
```bash
# Get peer (e.g. 72cc19c8435d662677b2ea627e649f39b5bc8abb@5.161.70.110:26656
echo "$(okp4d tendermint show-node-id)@$(curl ifconfig.me):$(curl -s $NODE/status | jq -r '.result.node_info.listen_addr' | cut -d':' -f3)"
```
### Wallet
```bash
# Get balance
okp4d q bank balances $ADDRESS
```

### Voting
```bash
# Vote
okp4d tx gov vote <PROPOSAL_ID> <yes|no> --from $WALLET --fees 5000uknow -y
```
```bash
# Check all voted proposals
okp4d q gov proposals --voter $ADDRESS
```

### Actions
```bash
# Edit validator
okp4d tx staking edit-validator --website="<YOUR_WEBSITE>" --details="<YOUR_DESCRIPTION>" --moniker="<YOUR_NEW_MONIKER>" --from=$WALLET --fees 5000uknow
```
```bash
# Unjail
okp4d tx slashing unjail --from $WALLET --fees 5000uknow
```
```bash
# Bond more tokens (if you want increase your validator stake you should bond more to your valoper address):
okp4d tx staking delegate $VALOPER <TOKENS_COUNT>uknow--from $WALLET --fees 5000uknow -y
```
```bash
# Undelegate
okp4d tx staking unbond $VALOPER <TOKENS_COUNT>uknow --from $WALLET --fees 5000uknow -y
```
```bash
# Send tokens. 1 token = 1000000 (Cosmos)
okp4d tx bank send $WALLET <WALLET_TO> <TOKENS_COUNT>uknow --fees 5000uknow
# e.g. okp4d tx bank send $WALLET cosmos10h3t6rtrjwxqlw0jgwc540rthuclhvrzhndkeg 1000000uknow --gas auto
```
```bash
# Change peers and seeds
peers="<PEERS>"
seeds="<SEEDS>"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/; s/^seeds *=.*/seeds = \"$seeds\"/" $HOME/.okp4d/config/config.toml
```
```bash
# Reset private validator file to genesis state and delete addrbook.json
okp4d tendermint unsafe-reset-all --home $HOME/.okp4d
```

### All validators info
```bash
# List of all active validators 
okp4d q staking validators -o json --limit=1000 \
| jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' \
| jq -r '.tokens + " - " + .description.moniker' \
| sort -gr | nl
```
```bash
# List of all inactive validators 
okp4d q staking validators -o json --limit=1000 \
| jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' \
| jq -r '.tokens + " - " + .description.moniker' \
| sort -gr | nl
```

### Another useful commands
```bash
# Root -> your node
su -l $USER_NAME
```
```bash
# Check internet connection
curl -sL yabs.sh | bash -s -- -fg
```
```bash
# Server load
sudo htop
```
```bash
# File structure
ncdu
```

## Delete node
```bash
sudo systemctl stop okp4d && \
sudo systemctl disable okp4d; \
sudo rm /etc/systemd/system/okp4d.service; \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .okp4d okp4d; \
sudo rm $(which okp4d)
```
