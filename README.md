# Namada infrastructure

## Infrastructures

#### Public RPC: 
```
https://rpc.shieldedexpedition-mohismstake.site/
```

#### Indexer Service: 
```
https://indexer.shieldedexpedition-mohismstake.site/block/last
```

#### Seed:
```
tcp://e1e4f80f1a4738f3a3983369c35e3d3fe3369b3f@154.53.45.59:26656
```

#### Peer:
```
tcp://e1e4f80f1a4738f3a3983369c35e3d3fe3369b3f@154.53.45.59:26656
```

#### Address book:
```
tcp://ip:port
```

#### Snapshot:
```
http://rpc.shieldedexpedition-mohismstake.site:12345/snapshot_testnetse.tar.gz
```

## Validator Setup

#### Recover key from registered account
```
namadaw derive --alias "ALIAS"
```

#### Check key address
```
namadaw find --alias "ALIAS"
```

#### Check balance well
```
namadac balance --owner "ALIAS"
```

#### Initialize validator
```
namadac init-validator \
 --alias "VAL_NAME" \
 --account-keys "KEY_NAME" \
 --signing-keys "KEY_NAME" \
 --commission-rate 0.1 \
 --max-commission-rate-change 0.1 \
 --email "EMAIL" \
```

#### Find validator key
```
namadac find-validator --tm-address=$(curl -s localhost:26657/status | jq -r .result.validator_info.address)
```

#### Self Bond
```
namada client bond --validator "VALIDATOR" --amount "AMOUNT"
```

#### Bond tokens to your validator
```
namadac bond --source "DELEGATOR" --validator "<Validator address>" --amount AMOUNT
```

## Validator management

#### Validator consensus state
```
namadac validator-state --validator "<Validator address>"
```

#### Unjail validator
```
namadac unjail-validator --validator  "<Validator address>"
```

#### Query a validator's bonded-stake
```
namadac bonded-stake
```

#### Deactivate validator
```
namadac deactivate-validator --validator "VALIDATOR"
```

#### Reactivate validator
```
namadac reactivate-validator --validator "VALIDATOR"
```

#### Query validator metadata
```
namadac validator-metadata --validator "VALIDATOR"
```

#### Change validator metadata
```
namadac change-metadata \
  --validator "VALIDATOR" \
  --website "WEBSITE_URL" \
  --description "DESCRIPTION" \
  --avatar "LINK_TO_AVATAR" \
  --discord-handle "DISCORD_HANDLE" \
  --email "EMAIL"
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

#### Active service
```
sudo chmod 755 /etc/systemd/system/namadad.service
sudo systemctl daemon-reload
sudo systemctl enable namadad
sudo systemctl start namadad && sudo journalctl -u namadad -n 1000 -f
```

#### Service commands
```
sudo service namadad start
sudo service namadad status
sudo service namadad stop
sudo service namadad restart
```
