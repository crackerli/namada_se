# Namada services

**Node Setup**

#### Install Pre-requisites software

<pre><code id="code1">
sudo apt update -y  
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler libudev-dev  
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y  
. $HOME/.cargo/env
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code1')">Copy</button>

#### Setup firewall
<pre><code id="code2">
sudo ufw allow 22  
sudo ufw allow 26656
sudo ufw allow 26657  (RPC)
sudo ufw allow 26660  (for namada metic)
sudo ufw allow 9100   (for node-exporter)
sudo ufw enable
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code2')">Copy</button>

#### Config enviornment
<pre><code id="code3">
vi ~/.bash_profile
export PATH=$HOME/.cargo/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/go/bin:$HOME/go/bin  
export BASE_DIR=$HOME/.local/share/namada  
export NAMADA_TAG="Namada Release Tag"    
export TM_HASH=v0.1.4-abciplus  
export CHAIN_ID="public-testnet-15.0dacadb8d663"  
export PUBLIC_IP="PUBLIC IP"
export IP_PORT="PUBLIC IP:26656"  
export VALIDATOR_ALIAS="VALIDATOR MONIKER"  
. "$HOME/.cargo/env"
source ~/.bash_profile
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code3')">Copy</button>

#### Build from source code
Build namada and copy to /usr/local/bin
<pre><code id="code4">
rm -rf $HOME/namada
cd $HOME && git clone https://github.com/anoma/namada && cd namada && git checkout $NAMADA_TAG
make build-release

cd $HOME && sudo cp "$HOME/namada/target/release/namada" /usr/local/bin/namada && sudo cp "$HOME/namada/target/release/namadac" /usr/local/bin/namadac && sudo cp "$HOME/namada/target/release/namadan" /usr/local/bin/namadan && sudo cp "$HOME/namada/target/release/namadaw" /usr/local/bin/namadaw && sudo cp "$HOME/namada/target/release/namadar" /usr/local/bin/namadar
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code4')">Copy</button>

Install consensus cometbft
<pre><code id="code5">
wget https://github.com/cometbft/cometbft/releases/download/v0.37.2/cometbft_0.37.2_linux_amd64.tar.gz
tar -xvf cometbft_0.37.2_linux_amd64.tar.gz
sudo cp cometbft /usr/local/bin/
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code5')">Copy</button>

Verify version
<pre><code id="code6">
cometbft version
0.37.2
namada --version
Namada v0.28.2
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code6')">Copy</button>

#### Copy pre-genesis folder to $BASE_DIR
<pre><code id="code7">
mkdir -p $BASE_DIR
sudo cp -r ~/pre-genesis $BASE_DIR
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code7')">Copy</button>

#### Join network
<pre><code id="code8">
cd $HOME && namadac utils join-network --chain-id $CHAIN_ID --genesis-validator $VALIDATOR_ALIAS
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code8')">Copy</button>

#### Modify $BASE_DIR/$CHAIN_ID/config.toml
<pre><code id="code9">
[ledger.cometbft.rpc]
laddr = "tcp://0.0.0.0:26657"
[ledger.cometbft.instrumentation]
prometheus = true
prometheus_listen_addr = ":26660"
[ledger.cometbft]
moniker = "<your-moniker-name>"
</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code9')">Copy</button>

**RPC, Peers, Seed, Addrbook, Genesis**

#### Stop the service and reset the data

<pre><code id="code1">sudo systemctl stop namada.service
cp $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data/priv_validator_state.json $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data $HOME/.local/share/namada
rm -rf $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code1')">Copy</button>

#### Download latest snapshot

Take the latest you need from [Snapshots](Snapshots)

<pre><code id="code2">link="&lt;above link&gt;"
curl -L $link | tar -I lz4 -xf - -C $HOME/.local/share/namada/shielded-expedition.88f17d1d14
mv $HOME/.local/share/namada/shielded-expedition.88f17d1d14/priv_validator_state.json.bac $HOME/.local/share/namada/shielded-expedition.88f17d1d14/priv_validator_state.json</code></pre>
<button onclick="copyToClipboard('#code2')">Copy</button>

#### Restart the service and check the log

<pre><code id="code3">sudo systemctl start namada.service && sudo journalctl -u namada.service -fn 100 -o cat</code></pre>
<button onclick="copyToClipboard('#code3')">Copy</button>

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
