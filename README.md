# Snapshot

Instructions

## Stop the service and reset the data

<pre><code id="code1">sudo systemctl stop namada.service
cp $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data/priv_validator_state.json $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data $HOME/.local/share/namada
rm -rf $HOME/.local/share/namada/shielded-expedition.88f17d1d14/cometbft/data</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code1')">Copy</button>

## Download latest snapshot

Take the latest you need from [Snapshots](Snapshots)

<pre style="background-color:red"><code id="code2">link="&lt;above link&gt;"
curl -L $link | tar -I lz4 -xf - -C $HOME/.local/share/namada/shielded-expedition.88f17d1d14
mv $HOME/.local/share/namada/shielded-expedition.88f17d1d14/priv_validator_state.json.bac $HOME/.local/share/namada/shielded-expedition.88f17d1d14/priv_validator_state.json</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code2')">Copy</button>

## Restart the service and check the log

<pre><code id="code3">sudo systemctl start namada.service && sudo journalctl -u namada.service -fn 100 -o cat</code></pre>
<button style="background:#3630a3;color:white" onclick="copyToClipboard('#code3')">Copy</button>

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
