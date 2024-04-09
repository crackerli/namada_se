# Namada services

## Recommend environments
<p style="background:black;color:white;padding:10px;border-radius:6px">
export PATH=$HOME/.cargo/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin<br />
export PATH=/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:$HOME/go/bin<br />
export BASE_DIR=$HOME/.local/share/namada<br />
export NAMADA_TAG="Namada Release Tag"<br />
export TM_HASH=v0.1.4-abciplus<br />
export CHAIN_ID="public-testnet-15.0dacadb8d663"<br />
export PUBLIC_IP="PUBLIC IP"<br />
export IP_PORT="PUBLIC IP:26656"<br />
export VALIDATOR_ALIAS="VALIDATOR MONIKER"<br />
</p>

## Run node as service
sudo vi /etc/systemd/system/namadad.service
<p style="background:black;color:white;padding:10px;border-radius:6px">
[Unit]
Description=namada
After=network-online.target

[Service]
User=namadanet
WorkingDirectory=/home/namadanet/.local/share/namada
Environment="NAMADA_LOG=info"
Environment="CMT_LOG_LEVEL=p2p:none,pex:error"
Environment="NAMADA_CMT_STDOUT=true"
ExecStart=/usr/local/bin/namada --base-dir=/home/namadanet/.local/share/namada node ledger run  
StandardOutput=syslog
StandardError=syslog
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
</p>

<p style="background:black;color:white;padding:10px;border-radius:6px">
sudo chmod 755 /etc/systemd/system/namadad.service  
sudo systemctl daemon-reload  
sudo systemctl enable namadad  
sudo systemctl start namadad && sudo journalctl -u namadad -n 1000 -f
</p>

### Service commands
<p style="background:black;color:white;padding:10px;border-radius:6px">
sudo service namadad start  
sudo service namadad status  
sudo service namadad stop   
sudo service namadad restart 
</p>

## Node Services

#### Public RPC: 
<p style="background:black;color:white;padding:10px;border-radius:6px">
https://namada-se.staking_power.com/
</p>

#### Indexer Service: 
<p style="background:black;color:white;padding:10px;border-radius:6px">
https://indexer.staking_power.com/
</p>

#### Genesis:
<p style="background:black;color:white;padding:10px;border-radius:6px">
https://files.somewhere.com/genesis.json
</p>

#### Seed:
<p style="background:black;color:white;padding:10px;border-radius:6px">
tcp://ip:port
</p>

#### Peer:
<p style="background:black;color:white;padding:10px;border-radius:6px">
tcp://ip:port
</p>

### Snapshot:
#### Stop the service and reset the data
<p style="background:black;color:white;padding:10px;border-radius:6px">
sudo systemctl stop namada.service
cp $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data/priv_validator_state.json $HOME/.local/share/namada/shielded-expedition.88f17d1d14/priv_validator_state.json.backup
rm -rf $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data $HOME/.local/share/namada/shielded-expedition.88f17d1d14/db $HOME/.local/share/namada/shielded-expedition.88f17d1d14/wasm
</p>

#### Download latest snapshot
<p style="background:black;color:white;padding:10px;border-radius:6px">
wget -P $BASE_DIR/somewhere https://files.somewhere.com/namada-snapshot.tar.gz
tar -zcvf namada-snapshot.tar.gz
</p>

#### Restart service
<p style="background:black;color:white;padding:10px;border-radius:6px">
sudo systemctl restart namadad && sudo journalctl -u namada.service -fn 100 -o cat
</p>

<script>
function copyToClipboard(element) {
  var text = document.querySelector(element).innerText;
  var elem = document.createElement("textarea");
  document.body.appendChild(elem);
  elem.value = text;
  elem.select();
  document.execCommand("copy");
  document.body.removeChild(elem);
  alert("Code copied to clipboard");
}
</script>
