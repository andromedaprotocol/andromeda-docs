---
description: The steps to successfully run a node on the Andromeda chain.
---

# Running a Node

### Install Prerequisites

{% hint style="success" %}
You can also check [this guide](https://aviaone.com/blockchains-service/andromeda-1-andromeda.html) that was created by one of our validators.
{% endhint %}

```bash
sudo apt-get update && sudo apt upgrade -y
sudo apt-get install make build-essential git jq chrony -y
```

```bash
sudo apt install gcc
```

### Increasing the default open files limit

This is to make sure that the nodes won't crash once the network grows larger and larger.

```bash
sudo su -c "echo 'fs.file-max = 65536' >> /etc/sysctl.conf"
sudo sysctl -p
```

### Installing Go 1.20.x

You can find GoLang 1.20.x from [here](https://go.dev/dl/).

### Updating Environment Variables&#x20;

```bash
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export GOBIN=$HOME/go/bin
export PATH=$PATH:/usr/local/go/bin:$GOBIN
EOF
source $HOME/.profile
```

Next we need to clone the source repo:

```bash
git clone https://github.com/andromedaprotocol/andromedad.git
cd andromedad
make install
```

## Initialize the Config Files

```bash
andromedad init <MONIKER> --chain-id andromeda-1
andromedad config chain-id andromeda-1
```

Now, your node environment should be ready to go! In order to sync up to the network, a few more parameters must be changed to proper specifics for the network:

```bash
cd ~/.andromeda/config/
```

#### Create new keys, or import existing wallet with BEP32 mnenomic phrase:

```bash
andromedad keys add <KEY_NAME> --keyring-backend os
```

To recover existing wallet:

```bash
andromedad keys add <KEY_NAME> --keyring-backend os --recover
```

### \`gentx\` Command

To generate your validators transaction (gentx) use the following command:

```
andromedad genesis gentx <WALLET> 100000000000uandr --chain-id andromeda-1
```

Then submit a pull request to the `mainnet` repo where you found the genesis.json

### Create environment Variable:

```bash
MY_VALIDATOR_ADDRESS=$(andromedad keys show <KEY_NAME> -a --keyring-backend os)
```

### Setting up persistent peers in config.toml

#### Add Persistent Peers to config.toml:

```
vim config.toml
```

Now connect to some peers nodes by setting `persistent_peers.`

#### Example

{% hint style="warning" %}
You can ask for seeds in the discord validator channel.
{% endhint %}

```
persistent_peers = "e4c2267b90c7cfbb45090ab7647dc01df97f58f9@andromeda-m.peer.stavr.tech:4376,258f523c96efde50d5fe0a9faeea8a3e83be22ca@seed.andromeda-1.andromeda.aviaone.com:1028"
```

#### Copy Genesis File:

```
git clone https://github.com/andromedaprotocol/mainnet
cd mainnet
cp genesis.json $HOME/.andromeda/config/
```

### Starting the Network

```bash
andromedad start
```

Your node should now be catching up to the current state of the network!

### Create Validator Command&#x20;

{% hint style="warning" %}
Send yourself some tokens from the Andromeda Faucet before proceeding.
{% endhint %}

```bash
andromedad tx staking create-validator \
  --amount=1000000 uandr \
  --pubkey=$(andromedad tendermint show-validator) \
  --moniker=<MONIKER> \
  --chain-id=andromeda-1 \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --gas="700000" \
  --from=<KEY_NAME>
```

Replace **\<MONIKER>** and **\<KEY\_NAME>** above.

#### Setup andromedad systemd service (copy and paste all to create the file service)

#### &#x20;Enable and activate the andromedad service.

```bash
sudo nano /etc/systemd/system/andromedad.service && sudo touch /etc/systemd/system/andromedad.service 
```

#### Insert content into andromedad.service:

```bash
[Unit]
Description=Andromeda Node
After=network-online.target

[Service]
User=root
ExecStart=/root/go/bin/andromedad start
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

#### Move file to systemd folder:

```bash
sudo mv /etc/systemd/system/andromedad.service /lib/systemd/system/
sudo systemctl enable andromedad.service && sudo systemctl start andromedad.service
```

#### Check info about node:

```bash
curl -s localhost:26657/status  | jq .result.sync_info.catching_up
#true output is syncing - false is synced
curl -s localhost:26657/status | jq .result.sync_info.latest_block_height
#this output is your last block synced
curl -s "http://:26657/status?"  | jq .result.sync_info.latest_block_height
#this output the public node last block synced
```
