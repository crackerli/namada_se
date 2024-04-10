# Namada infrastructure & Validator instructions

## Infrastructures

#### Public RPC: 
```
https://namada-se.staking_power.com/
```

#### Indexer Service: 
```
https://indexer.staking_power.com/
```

#### Seed:
```
tcp://ip:port
```

#### Peer:
```
tcp://ip:port
```

#### Snapshot:
```
https://files.somewhere.com/namada-snapshot.tar.gz
```

## Validator Setup

#### Recover key from registered account
```
namadaw derive --alias "${ALIAS}"
```

#### check key address
```
namadaw find --alias "${ALIAS}"
```

#### check balance well
```
namadac balance --owner "${ALIAS}"
```

#### find validator key
```
namadac find-validator --tm-address=$(curl -s localhost:26657/status | jq -r .result.validator_info.address)
```

#### initialize validator
```
namadac  init-validator \
 --alias "${VAL_NAME}" \
 --account-keys "${KEY_NAME}" \
 --signing-keys "${KEY_NAME}" \
 --commission-rate 0.1 \
 --max-commission-rate-change 0.1 \
 --email "${EMAIL}" \
```

#### bond tokens to your validator
```
namadac  bond \
 --source "${KEY_NAME}" \
 --validator "<Validator address>" \
 --amount 1000 \
```

## Validator management

#### validator consensus state
```
namadac validator-state --validator "<Validator address>"
```

#### unjail validator
```
namadac unjail-validator --validator  "<Validator address>"
```

#### claim rewards
```
namadac claim-rewards --validator "<Validator address>"
```

## Run node as Service
```
sudo tee /etc/systemd/system/namadad.service << EOF
[Unit]
Description=Namada Node
After=network.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Type=simple
ExecStart=/usr/local/bin/namada --base-dir=$HOME/.local/share/namada node ledger run
Environment=NAMADA_CMT_STDOUT=true
Environment=TM_LOG_LEVEL=p2p:none,pex:error
RemainAfterExit=no
Restart=on-failure
RestartSec=10s
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
