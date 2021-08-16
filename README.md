# LUNA Validator Setup Guide

**LAST UPDATED:**

- Aug 16, 2021

- 1:39 PM PST

### Similar Guides


- [block42 May 2020 Guide] (https://medium.com/block42-blockchain-company/how-to-setup-a-terra-luna-validator-node-860d8ea7aea2)

### Important Columbus-5 & Bombay-09 Update

- [Columbus-5 & Bombay-09 Update] (https://medium.com/terra-money/columbus-5-bombay-09-update-4fdf94da0fe6)
- [Bombay-9 Repo] (https://github.com/terra-money/testnet/tree/master/bombay-9)
- [Columbus-5 Mainnet Upgrade Proposal and Recommendations] (https://agora.terra.money/t/columbus-5-mainnet-upgrade-proposal-and-recommendations/1840)
- [Columbus 5 Upgrade Instructions] (https://github.com/terra-money/mainnet/wiki/Columbus-5-Upgrade-Instructions)



### OS/Hardware Requirements
- Ubuntu 20.10 (Groovy Gorilla)
- AWS EC2 r5n xlarge
- 4 cpu's
- 32 GiB
- 1 TB storage

### Ports
- open Port 22 for SSH
- open Port 26656 for Peer TCP Traffic

### Create New User (only if necessary) 

```
sudo apt update
```

```
sudo apt upgrade
```

```
sudo apt install -y build-essential
```

```
USER = <user>
```

```
adduser -aG sudo -m -U $USER
``` 

### Set Variables
```
USER = terra
```
The username of the ubuntu user. This step is only required for the creation of new user.


```
MONIKER = terra-validator
```
The name of your validator.

**<-- SET THE REST OF THESE AFTER YOU LOG OUT / LOG BACK IN -->**

```
GENESIS_URLs:

# tequila-0004: 
https://raw.githubusercontent.com/terra-money/testnet/master/tequila-0004/genesis.json

# bombay-9: 
https://raw.githubusercontent.com/terra-money/testnet/master/bombay-9/genesis.json 
```
Quick download of Genesis Doc.

```
SNAPSHOT_FILE_URL = https://get.quicksync.io/tequila-4-default.20210801.0940.tar.lz4 
```
Large file download of chain snapshot. Come back in ~2 hours.

```
ADDRESS_BOOK_URL = https://network.terra.dev/testnet/addrbook.json 
```
Quick download of Address Book.

```
ORACLE_WALLET_ADDRESS =
```

```
VALIDATOR_WALLET_NAME =
```

```
CHAIN_ID=
```
tequila-0004 for the testnet

columbus-4 for mainnet

```
VALIDATOR_WALLET_ADDRESS =
```

```
LUNA_AMOUNT=
```
1 LUNA = 1000000 uLUNA

When we say LUNA AMOUNT, typically uLuna

### Install Dependencies
Login to USER.

```
sudo apt install -y build-essential wget liblz4-tool aria2 jq gcc g++ make aria2
```


### Install NodeJS and Yarn

Installing it via apt-get without the below step will install an older version. You want to install Nodejs LTS always [(currently v14)](https://github.com/nodesource/distributions/blob/master/README.md#debinstall):

```
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - && curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null && echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list && sudo apt-get update && sudo apt install -y yarn nodejs npm && sudo npm i -g pm2
```

### Install Go and golangcli-lint

```
wget -c https://golang.org/dl/go1.16.6.linux-amd64.tar.gz -O - | sudo tar -xz -C /usr/local
```

```
curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sudo sh -s -- -b /usr/local/go/bin/ v1.41.1
```

### Export Paths

```
vim ~/.bashrc
```
At the bottom of the bashrc file, add:

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
export PATH=$PATH:/usr/local/go/bin
```

```
source ~/.bashrc
```

### Install Terra Core

```
git clone https://github.com/terra-project/core/
```

```
cd core
```

```
git checkout v0.4.6
```

```
make install
```

### Prepare Node for Sync
```
cd ~
```

```
terrad init <moniker>
```

```
curl https://raw.githubusercontent.com/terra-money/testnet/master/tequila-0004/genesis.json > $HOME/.terrad/config/genesis.json
```

Add in Seed and Persistent Peers values into Config.toml File:

```
vim ~/.terrad/config/config.toml
# Seed = '341f51bf381566dfef0fc345c2aa882cbeebd320@public-seed2.terra.dev:36656'
# Persistent Peers = '9ab68536fdaed15ea4377e8d9b2c56cb67057287@15.164.0.235:26656,d2033687c5e87e1a875bb37239cbb4f08b2493eb@3.35.148.111:26656'
```
Add in Minimum Gas Price values in App.toml File:

```
vim ~/.terrad/config/app.toml
```

```
minimum-gas-prices = "0.01133uluna,0.15uusd,0.104938usdr,169.77ukrw,428.571umnt,0.125ueur,0.98ucny,16.37ujpy,0.11ugbp,10.88uinr,0.19ucad,0.14uchf,0.19uaud,0.2usgd,4.62uthb,1.25usek,1.25unok,1.25udkk”
```

### Download Snapshot

```
cd ~/.terrad
```

Get most recent snapshot from [Chainlayer Quicksync] (https://terra.quicksync.io/).

```
aria2c -x5 https://get.quicksync.io/tequila-4-default.20210801.0940.tar.lz4 
```

```
lz4 -c -d {SNAPSHOT_FILE} | tar xf -
```

### Begin Node Sync

```
curl https://network.terra.dev/testnet/addrbook.json > $HOME/.terrad/config/addrbook.json && terrad start
```

### Begin pm2 Process Manager

```
cd ~
```

```
touch runNode.sh
```

```
chmod +x runNode.sh
```
Add into runNode file:

```
vim runNode.sh

# Insert: 
#!/bin/sh
terrad start

```

```
touch terra-node.ecosystem.config.js
```
Add into terra-node.ecosystem.config file:

```
vim terra-node.ecosystem.config.js
# Insert:
module.exports = { apps : [ { name: "terra-node", script: "/home/{USER}/runNode.sh", exec_mode: "fork", exec_interpreter: "bash"} ] }
```
Start pm2 process manager:

```
pm2 start ./terra-node.ecosystem.config.js
```
### Managing Node via pm2
```
pm2 save
```

```
pm2 startup
```

```
pm2 status
```

```
pm2 logs
```

Check to see if your node is catching up (should be false):

```
curl -Ss localhost:26657/status | jq -er ".result.sync_info"
```


### Register Validator Node

Create Validator Wallet:

- Validator wallet name (i.e., address)
- Validator wallet password
- Validator wallet mnemonic

```
terracli keys add <key_name>
```

To get validator address:

```
terrad tendermint show-validator

terracli keys list 
```

Configure Node:

```
terracli config trust-node true

terracli config node  tcp://localhost:26657
```

Change to relevant chain-id:

```
terracli config chain-id tequila-0004
```

If on TestNet, add LUNA into validator via [Faucet](https://faucet.terra.money/). Use the address from `terracli keys list`.

Change the minimum gas price in the `~/config/app.toml` file:

```
minimum-gas-prices = "0.01133uluna,0.15uusd,0.104938usdr,169.77ukrw,428.571umnt,0.125ueur,0.98ucny,16.37ujpy,0.11ugbp,10.88uinr,0.19ucad,0.14uchf,0.19uaud,0.2usgd,4.62uthb,1.25usek,1.25unok,1.25udkk”
```

In the `terracli tx staking-create validator`command, add fees:

```
--fees=30000uluna
```

Restart terrad:

```
pm2 restart terra-node
```

`terracli tx staking-create validator` command should look something like this:

```
terracli tx staking create-validator \
    --amount=5000000uluna \
    --pubkey=$(terrad tendermint show-validator) \
    --moniker="petermatt" \
    --chain-id tequila-0004 \
    --from=validator \
    --commission-rate="0.10" \
    --commission-max-rate="0.20" \
    --commission-max-change-rate="0.01" \
    --min-self-delegation="1" \
    --fees=30000uluna
```


**Sometimes this might take a sec, code 19 doesn’t mean its broken (at least we think).
**

### Confirm Transactions for Validator

This command should show your validator pub key:

```
terracli query tendermint-validator-set | grep "$(terrad tendermint show-validator)"
```


### Set up Feeder

[Github Repo Here](https://github.com/terra-money/oracle-feeder).

Create Feeder Wallet:

- Feeder wallet name (i.e., address)
- Feeder wallet password
- Feeder wallet mnemonic

```
terracli keys add <feeder>

terracli keys show <feeder>
```

```
terracli tx oracle set-feeder <feeder-address> --from <validator-name> --fees 30000uluna
```

```
terracli tx send <validator-address> <feeder-address> <luna-amount>uluna --fees 30000uluna
```

### Set up Oracle

[Github Repo Here](https://github.com/terra-money/oracle-feeder/tree/main/feeder).

Create Oracle Wallet:

- Oracle wallet name (i.e., address)
- Oracle wallet password
- Oracle wallet mnemonic

```
git clone https://github.com/terra-money/oracle-feeder && cd oracle-feeder/price-server && npm install
```

Copy sample config file:

```
cd config
```

```
cp default-sample.js default.js
```

Make edits:

```
vim ./config/default.js

# Make Edits - ensure to have premium API keys.

# Price is available at `http://127.0.0.1:8532/latest`.

fiatProvider: {
    fallbackPriority: ['currencylayer', 'alphavantage', 'fixer', 'exchangerate', 'bandprotocol'],
    currencylayer: {
      symbols: fiatSymbols,
      interval: 60 * 1000,
      timeout: 5000,
      // https://currencylayer.com/product
      // recommend: business subscription(60second Updates): $79.99/month
      apiKey: 'fffe154e3f28482b9c95ae82942a27a4', // necessary
    },
    bandprotocol: {
      // DKK is not supported for bandprotocol
      symbols: fiatSymbols.filter(v => !v.includes('DKK') && !v.includes('PHP')),
      interval: 60 * 1000,
      timeout: 5000,
      // https://data.bandprotocol.com/
    },
    exchangerate: {
      symbols: fiatSymbols,
      interval: 60 * 1000,
      timeout: 5000,
      // https://exchangerate.host/
    },
    fixer: {
      symbols: fiatSymbols,
      interval: 60 * 1000,
      timeout: 5000,
      // https://fixer.io/product
      // recommend: professional plus(60second Updates): $80/month
      apiKey: '53ffecdae8919ece2c96de9e8c122516', // necessary
    },
    alphavantage: {
      symbols: fiatSymbols,
      interval: 60 * 1000,
      timeout: 5000,
      // https://www.alphavantage.co/premium/
      // recommend: 120 API request per minute: $49.99/month
      apiKey: 'S78O66J9WV9IURWJ', // necessary
    },
  },
```

```
npm install @opentelemetry && npm run start
```

### Set the Oracle Price Feeder

```
terracli tx oracle set-feeder {ORACLE WALLET ADDRESS} --from={VALIDATOR_WALLET_NAME} --gas-prices="0.5uluna" --chain-id {CHAIN_ID}
```


### Launch Feeder

```
cd ~/oracle-feeder/feeder && npm install && npm start update-key

# Enter Oracle mnemonic
```

Start voting:

```
npm start vote -- \
   --source http://localhost:8532/latest \
   --lcd https://lcd.terra.dev \
   --chain-id tequila-0004 \
   --validator validator\
   --gas-prices 169.77ukrw \
   --password "helloworld123"
   --fees 30000uluna

```

**Done!**

### Need to Figure Out + Errors:

- It is confirmed that if the staking amount is not enough then gentx will not generate the validator and it will lead to this error.

- Error: error during handshake: error on replay: validator set is nil in genesis and still empty after InitChain

### Troubleshooting
- Error: - It is confirmed that if the staking amount is not enough then gentx will not generate the validator and it will lead to this error.
  Noticed this error here: https://github.com/terra-money/core/issues/131. By making sure you have the right genesis.json file set, this should go away.

### Notes

- For Bombay, you can increase (a lot) the values of max_subscription_clients, max_num_inbound_peers, 
max_num_outbound_peers and max_open_connections in config.toml to speed up the process
like 100/130/130/30
