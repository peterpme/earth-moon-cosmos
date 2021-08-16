# Columbus 5 / Bombay 9 Setup

Columbus 5 is the new version Terra coming out in September. Bombay is the name of the testnet

### Resources / Links
- [Columbus-5 & Bombay-9 Update] (https://medium.com/terra-money/columbus-5-bombay-09-update-4fdf94da0fe6)
- [Bombay-9 Repo] (https://github.com/terra-money/testnet/tree/master/bombay-9)
- [Columbus-5 Mainnet Upgrade Proposal and Recommendations] (https://agora.terra.money/t/columbus-5-mainnet-upgrade-proposal-and-recommendations/1840)
- [Columbus 5 Upgrade Instructions] (https://github.com/terra-money/mainnet/wiki/Columbus-5-Upgrade-Instructions)

### OS/Hardware Requirements
- Ubuntu 20.10 (Groovy Gorilla)
- AWS EC2 r5n xlarge
- 4 cpu's
- 32 GiB
- 1 TB storage

### Genesis URLs

```sh
# bombay-9 testnet
https://raw.githubusercontent.com/terra-money/testnet/master/bombay-9/genesis.json
```

### Snapshot

Snapshot is only about 2gb at the time of running so there is no quicksync url.

### Persistent Peers
```sh
persistent_peers = "eb4168239744007adcce028a397fc2addf4b2520@54.150.118.40:36656,2e13408cbe993a291caf02946e902753f7f95a71@3.34.120.243:26656,67f83663158a908b1a0784e642eafde880dd2929@65.21.157.38:26656,f9cb325f1ca9296c2853c2f416991e34927e23f7@207.180.213.123:26656,c7fdeca4135e56149f5f5d84462c9eb9f059edb8@52.78.140.220:26656,bdc57c5a7f11040bed560fceb7d9b17c117e3423@193.239.85.118:26656,05bf2a0786c34f07452f21a0d4fc00061224b59f@138.201.60.238:26656,e6be82b4a659964fad27ee14f844c222fe9abadf@104.197.21.152:26656,2c7a1c74c793456209188a59fc01d9c7f139f5be@34.136.129.123:26656,9762192a79f88f37419d32f164a88e05ce024aec@168.119.150.243:26656"
```
