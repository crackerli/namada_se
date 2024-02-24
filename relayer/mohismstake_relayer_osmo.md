# Description:   
**Category:**  
Operating IBC/ Interoperability infrastructure  

**Description:**  
Operate a Shielded Expedition-compatible Osmosis testnet relayer

**Evidence:**  
Created channel:  
Namada SE    <->  Osmosis osmo-test-5  
channel-337  <->  channel-5847

SE account:  
_tpknam1qzyh9jt3edn04rha3d68yc0c2zzcjgppa5gx5l78rmf626eqypvxz7trv69_  
_transfer/channel-337/uosmo: 1000000_

# Prepare relayer accounts for Namada SE and Osmosis osmo-test-5
environments
```
export HERMES_CONFIG="$HOME/.hermes/config.toml"   
export RPC="http://154.53.45.59:26657"  
export mohismstake_se_relayer="tnam1qrzq7qz6u9clvkg2qp90slglemy96eqfyg4mhx4k"  
export mohismstake_osmo_relayer="osmo14qsp9633yhx0h6x9wr8qc4vvhxzl4xqrq98a69"
```
check addresses
```
namadaw find --alias mohismstake_se_relayer
Found transparent keys:
  Alias "mohismstake_se_relayer" (encrypted):
    Public key hash: C40F005AE171F6590A004AF87D1FCEC85D640922
    Public key: tpknam1qzyh9jt3edn04rha3d68yc0c2zzcjgppa5gx5l78rmf626eqypvxz7trv69
Found transparent address:
  "mohismstake_se_relayer": Implicit: tnam1qrzq7qz6u9clvkg2qp90slglemy96eqfyg4mhx4k
```
```
osmosisd keys show mohismstake_osmo_relayer
Enter keyring passphrase (attempt 1/3):
- address: osmo14qsp9633yhx0h6x9wr8qc4vvhxzl4xqrq98a69
  name: mohismstake_osmo_relayer
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"A8tbTBE5OhIbEbzgLWRSrY0i98dumsFzylcCYxnrM/49"}'
  type: local
```
# Install Hermes
## Download hermes 1.7.4+38f41c6
```
wget https://github.com/heliaxdev/hermes/releases/download/v1.7.4-namada-beta7/hermes-v1.7.4-namada-beta7-x86_64-unknown-linux-gnu.tar.gz
tar -xvf hermes-v1.7.4-namada-beta7-x86_64-unknown-linux-gnu.tar.gz
sudo mv hermes /usr/local/bin
hermes --version
hermes 1.7.4+38f41c6
```
## Create hermesd service
```
sudo tee /usr/lib/systemd/user/hermesd.service > /dev/null <<EOF
[Unit]
Description=Hermes Daemon Service
After=network.target
StartLimitIntervalSec=60
StartLimitBurst=3

[Service]
Type=simple
Restart=always
RestartSec=30
ExecStart=/usr/local/bin/hermes --config $HOME/.hermes/config.toml start 

[Install]
WantedBy=default.target
EOF
```
systemctl --user daemon-reload  
systemctl --user enable hermesd  
  
## Configure hermes
mkdir $HOME/.hermes  
vi $HOME/.hermes/config.toml
```
...
[[chains]]
id = 'shielded-expedition.88f17d1d14'
type = 'Namada'
rpc_addr = 'http://154.53.45.59:26657'
grpc_addr = 'http://154.53.45.59:9090'
event_source = { mode = 'push', url = 'ws://154.53.45.59:26657/websocket', batch_delay = '500ms' }
account_prefix = ''
key_name = 'mohismstake_se_relayer'
store_prefix = 'ibc'
gas_price = { price = 0.0001, denom = 'tnam1qxvg64psvhwumv3mwrrjfcz0h3t3274hwggyzcee' }
#gas_multiplier = 1.2
#default_gas = 40000000
#max_gas = 20000000000
rpc_timeout = '30s'

[[chains]]
id = 'osmo-test-5'
type = 'CosmosSdk'
rpc_addr = 'http://127.0.0.1:26657'  # set the IP and the port of the chain
grpc_addr = 'http://127.0.0.1:9090'
event_source = { mode = 'push', url = 'ws://127.0.0.1:26657/websocket', batch_delay = '500ms' }
account_prefix = 'osmo'
key_name = 'mohismstake_osmo_relayer'
address_type = { derivation = 'cosmos' }
store_prefix = 'ibc'
default_gas = 400000
max_gas = 120000000
gas_price = { price = 0.0025, denom = 'uosmo' }
gas_multiplier = 1.2
max_msg_num = 30
...
```
## Start hermesd service
systemctl --user start hermesd 

## Create IBC relayer accounts for hermes
echo "bean swap girl seek neck nice sponsor labor breeze net avoid puzzle clip december glass stool dilemma identify crime brave hat make such hard" > ./mnemonic  

hermes --config $HERMES_CONFIG keys add --chain shielded-expedition.88f17d1d14 --key-file $HOME/.local/share/namada/shielded-expedition.88f17d1d14/wallet.toml  
```
SUCCESS Added key 'mohismstake_se_relayer' (tnam1qrzq7qz6u9clvkg2qp90slglemy96eqfyg4mhx4k) on chain shielded-expedition.88f17d1d14
```

hermes --config $HERMES_CONFIG keys add --chain osmo-test-5 --mnemonic-file ./mnemonic
```
SUCCESS Restored key 'mohismstake_osmo_relayer' (osmo14qsp9633yhx0h6x9wr8qc4vvhxzl4xqrq98a69) on chain osmo-test-5
```
# Create IBC channel
```
hermes --config $HERMES_CONFIG \
  create channel \
  --a-chain shielded-expedition.88f17d1d14 \
  --b-chain osmo-test-5 \
  --a-port transfer \
  --b-port transfer \
  --new-client-connection --yes
```
```
2024-02-24T00:35:24.424307Z  INFO ThreadId(01) running Hermes v1.7.4+38f41c6
2024-02-24T00:35:24.984045Z  INFO ThreadId(01) Creating new clients, new connection, and a new channel with order ORDER_UNORDERED
2024-02-24T00:35:56.920520Z  INFO ThreadId(01) foreign_client.create{client=osmo-test-5->shielded-expedition.88f17d1d14:07-tendermint-0}: 🍭 client was created successfully id=07-tendermint-1169
2024-02-24T00:36:05.742640Z  INFO ThreadId(01) foreign_client.create{client=shielded-expedition.88f17d1d14->osmo-test-5:07-tendermint-0}: 🍭 client was created successfully id=07-tendermint-2284
2024-02-24T00:36:28.439874Z  INFO ThreadId(01) 🥂 shielded-expedition.88f17d1d14 => OpenInitConnection(OpenInit { Attributes { connection_id: connection-508, client_id: 07-tendermint-1169, counterparty_connection_id: None, counterparty_client_id: 07-tendermint-2284 } }) at height 0-51896
2024-02-24T00:37:12.343882Z  INFO ThreadId(01) 🥂 osmo-test-5 => OpenTryConnection(OpenTry { Attributes { connection_id: connection-2160, client_id: 07-tendermint-2284, counterparty_connection_id: connection-508, counterparty_client_id: 07-tendermint-1169 } }) at height 5-5587007
2024-02-24T00:37:16.761847Z  WARN ThreadId(01) foreign_client.wait_and_build_update_client_with_trusted{client=shielded-expedition.88f17d1d14->osmo-test-5:07-tendermint-2284 target_height=0-51900}:foreign_client.build_update_client_with_trusted{client=shielded-expedition.88f17d1d14->osmo-test-5:07-tendermint-2284 target_height=0-51900}:foreign_client.solve_trusted_height{client=shielded-expedition.88f17d1d14->osmo-test-5:07-tendermint-2284 target_height=0-51900}: resolving trusted height from the full list of consensus state heights for target height 0-51900; this may take a while
2024-02-24T00:37:55.989467Z ERROR ThreadId(01) failed ConnOpenAck ConnectionSide { chain: BaseChainHandle { chain_id: shielded-expedition.88f17d1d14 }, client_id: 07-tendermint-1169, connection_id: connection-508 }: tx response event consists of an error: The transaction was invalid: Event Event { event_type: Applied, level: Tx, attributes: {"inner_tx": "{\"gas_used\":{\"sub\":22185355},\"changed_keys\":[{\"segments\":[{\"AddressSeg\":\"tnam1qcqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqvtr7x4\"},{\"StringSeg\":\"connections\"},{\"StringSeg\":\"connection-508\"}]}],\"vps_result\":{\"accepted_vps\":[],\"rejected_vps\":[\"tnam1qcqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqvtr7x4\"],\"gas_used\":{\"max\":{\"sub\":129599},\"rest\":[{\"sub\":0},{\"sub\":0}]},\"errors\":[[\"tnam1qcqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqvtr7x4\",\"IBC native VP: IBC action error: IBC context error: ICS03 Connection error: invalid connection state: expected `INIT`, actual `OPEN`\"]],\"invalid_sig\":false},\"initialized_accounts\":[],\"ibc_events\":[{\"event_type\":\"connection_open_ack\",\"attributes\":{\"connection_id\":\"connection-508\",\"client_id\":\"07-tendermint-1169\",\"counterparty_client_id\":\"07-tendermint-2284\",\"counterparty_connection_id\":\"connection-2160\"}},{\"event_type\":\"message\",\"attributes\":{\"module\":\"ibc_connection\"}}],\"eth_bridge_events\":[]}", "height": "51904", "log": "", "info": "Check inner_tx for result.", "code": "2", "gas_used": "2219", "hash": "6B18A74282271AA9ACE69931A498AA7F43A68D1D8D1956DD85C29BD1E54BEE24"} }
2024-02-24T00:38:16.236267Z  INFO ThreadId(01) 🥂 osmo-test-5 => OpenConfirmConnection(OpenConfirm { Attributes { connection_id: connection-2160, client_id: 07-tendermint-2284, counterparty_connection_id: connection-508, counterparty_client_id: 07-tendermint-1169 } }) at height 5-5587023
2024-02-24T00:38:19.507461Z  INFO ThreadId(01) connection handshake already finished for Connection { delay_period: 0ns, a_side: ConnectionSide { chain: BaseChainHandle { chain_id: shielded-expedition.88f17d1d14 }, client_id: 07-tendermint-1169, connection_id: connection-508 }, b_side: ConnectionSide { chain: BaseChainHandle { chain_id: osmo-test-5 }, client_id: 07-tendermint-2284, connection_id: connection-2160 } }
2024-02-24T00:38:38.201643Z  INFO ThreadId(01) 🎊  shielded-expedition.88f17d1d14 => OpenInitChannel(OpenInit { port_id: transfer, channel_id: channel-337, connection_id: None, counterparty_port_id: transfer, counterparty_channel_id: None }) at height 0-51908
2024-02-24T00:39:00.828504Z  INFO ThreadId(01) 🎊  osmo-test-5 => OpenTryChannel(OpenTry { port_id: transfer, channel_id: channel-5847, connection_id: connection-2160, counterparty_port_id: transfer, counterparty_channel_id: channel-337 }) at height 5-5587034
2024-02-24T00:39:37.532998Z  INFO ThreadId(01) 🎊  shielded-expedition.88f17d1d14 => OpenAckChannel(OpenAck { port_id: transfer, channel_id: channel-337, connection_id: connection-508, counterparty_port_id: transfer, counterparty_channel_id: channel-5847 }) at height 0-51913
2024-02-24T00:40:01.220293Z  INFO ThreadId(01) 🎊  osmo-test-5 => OpenConfirmChannel(OpenConfirm { port_id: transfer, channel_id: channel-5847, connection_id: connection-2160, counterparty_port_id: transfer, counterparty_channel_id: channel-337 }) at height 5-5587049
2024-02-24T00:40:04.500025Z  INFO ThreadId(01) channel handshake already finished for Channel { ordering: ORDER_UNORDERED, a_side: ChannelSide { chain: BaseChainHandle { chain_id: shielded-expedition.88f17d1d14 }, client_id: 07-tendermint-1169, connection_id: connection-508, port_id: transfer, channel_id: channel-337, version: None }, b_side: ChannelSide { chain: BaseChainHandle { chain_id: osmo-test-5 }, client_id: 07-tendermint-2284, connection_id: connection-2160, port_id: transfer, channel_id: channel-5847, version: None }, connection_delay: 0ns }
SUCCESS Channel {
    ordering: Unordered,
    a_side: ChannelSide {
        chain: BaseChainHandle {
            chain_id: ChainId {
                id: "shielded-expedition.88f17d1d14",
                version: 0,
            },
            runtime_sender: Sender { .. },
        },
        client_id: ClientId(
            "07-tendermint-1169",
        ),
        connection_id: ConnectionId(
            "connection-508",
        ),
        port_id: PortId(
            "transfer",
        ),
        channel_id: Some(
            ChannelId(
                "channel-337",
            ),
        ),
        version: None,
    },
    b_side: ChannelSide {
        chain: BaseChainHandle {
            chain_id: ChainId {
                id: "osmo-test-5",
                version: 5,
            },
            runtime_sender: Sender { .. },
        },
        client_id: ClientId(
            "07-tendermint-2284",
        ),
        connection_id: ConnectionId(
            "connection-2160",
        ),
        port_id: PortId(
            "transfer",
        ),
        channel_id: Some(
            ChannelId(
                "channel-5847",
            ),
        ),
        version: None,
    },
    connection_delay: 0ns,
}
```
# IBC transfer between SE and osmo-test-5
## Check balances before
```
namadac balance --owner mohismstake_se_relayer --node $RPC
naan: 1780.987509
```
```
osmosisd query bank balances osmo14qsp9633yhx0h6x9wr8qc4vvhxzl4xqrq98a69
- amount: "199985749"
  denom: uosmo
pagination:
  next_key: null
  total: "0"
```

## Transfer from SE to osmo-test-5
```
namadac --base-dir $HOME/.local/share/namada \
    ibc-transfer \
    --amount 1 \
    --source mohismstake_se_relayer \
    --receiver osmo14qsp9633yhx0h6x9wr8qc4vvhxzl4xqrq98a69 \
    --token naan \
    --channel-id channel-337 \
    --node http://154.53.45.59:26657 \
    --memo tpknam1qzyh9jt3edn04rha3d68yc0c2zzcjgppa5gx5l78rmf626eqypvxz7trv69
Enter your decryption password: 
Transaction added to mempool.
Wrapper transaction hash: 874E798ADC0A06AC16EAD6348BD430A862F8EEC7820FF0FC59D822CA6B511A5B
Inner transaction hash: 2C306D0D879D976D3E6096EC156F1D35A5A327C54D0C79F0AF3B82C87DFA8AAE
Wrapper transaction accepted at height 51982. Used 26 gas.
Waiting for inner transaction result...
Transaction was successfully applied at height 51983. Used 6193 gas.
```
check balance after
```
osmosisd query bank balances osmo14qsp9633yhx0h6x9wr8qc4vvhxzl4xqrq98a69
balances:
- amount: "1"
  denom: ibc/B7B5504A1CACDE4DFCAAD53F2233F38272F469DAB01D4EE2FF6C6477F3E88E3D
- amount: "199985749"
  denom: uosmo
pagination:
  next_key: null
  total: "0"
```

## Transfer from osmo-test-5 to SE
```
osmosisd tx ibc-transfer transfer \
  transfer \
  channel-5847 \
  tnam1qrzq7qz6u9clvkg2qp90slglemy96eqfyg4mhx4k \
  1000000uosmo \
  --from mohismstake_osmo_relayer \
  --gas auto \
  --gas-prices 0.025uosmo \
  --gas-adjustment 1.2 \
  --node http://127.0.0.1:26657 \
  --home $HOME/.osmosisd \
  --chain-id osmo-test-5 \
  --yes
Enter keyring passphrase (attempt 1/3):
gas estimate: 116655
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: '[]'
timestamp: ""
tx: null
txhash: 9B37DB3795398DC5EC63813E0C05F694D8F0B5E66746BB358993E117521E9BC9
```
check balance after
```
namadac balance --owner mohismstake_se_relayer --node $RPC
naan: 1777.487509
transfer/channel-337/uosmo: 1000000
```
