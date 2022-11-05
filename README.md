# state_sync

```
SNAP_RPC="http://95.217.207.236:24557"
Name_bin="hid-noded"
Name_config_file=".hid-node"
Name_service="hypersingd"
peers="20e40949206d9d991274bfa388af4f77b7da0de1@65.108.66.34:24556"
```

```
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

```

```
sudo systemctl stop $Name_service && $Name_bin tendermint unsafe-reset-all --home $HOME/$Name_config_file --keep-addr-book

```

```
sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/$Name_config_file/config/config.toml

```
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml

```
```
sudo systemctl restart $Name_service
journalctl -fu $Name_service -o cat

```
