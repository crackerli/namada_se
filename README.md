# Namada infrastructure & Validator instructions

## Infrastructures

#### Public RPC: 
<pre style="background:black;color:white"><code id="code_rpc">
https://namada-se.staking_power.com/
</code></pre>

#### Indexer Service: 
<pre style="background:black;color:white"><code id="code_indexer">
https://indexer.staking_power.com/
</code></pre>

#### Seed:
<pre style="background:black;color:white"><code id="code_seed">
tcp://ip:port
</code></pre>

#### Peer:
<pre style="background:black;color:white"><code id="code_peer">
tcp://ip:port
</code></pre>

#### Snapshot:
<pre style="background:black;color:white"><code id="code_snapshot">
https://files.somewhere.com/namada-snapshot.tar.gz
</code></pre>

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
 --validator "<Validator address tham1...>" \
 --amount 1000 \
```
