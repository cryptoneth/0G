```
sudo apt update
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y

```

```
cd $HOME && \
ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
```
sudo apt install git
git clone -b v0.1.0 https://github.com/0glabs/0g-chain.git
cd 0g-chain
make install
0gchaind version
```
```
echo 'export MONIKER="crypton"' >> ~/.bash_profile
```
جای کریپتون اسم نود خودتون رو بنویسید یادتون نره******
```
echo 'export CHAIN_ID="zgtendermint_16600-1"' >> ~/.bash_profile
echo 'export WALLET_NAME="wallet"' >> ~/.bash_profile
echo 'export RPC_PORT="26657"' >> ~/.bash_profile
source $HOME/.bash_profile

```

```
cd $HOME
0gchaind config chain-id $CHAIN_ID
0gchaind config node tcp://localhost:$RPC_PORT
0gchaind config keyring-backend file
0gchaind init $MONIKER --chain-id $CHAIN_ID
```
```
wget -P ~/.0gchain/config https://github.com/0glabs/0g-chain/releases/download/v0.1.0/genesis.json
```
```
curl -o $HOME/.0gchain/config/addrbook.json https://zerog.snapshot.nodebrand.xyz/addrbook.json
```
---------------------------------------------
```
SEEDS="c4d619f6088cb0b24b4ab43a0510bf9251ab5d7f@54.241.167.190:26656,44d11d4ba92a01b520923f51632d2450984d5886@54.176.175.48:26656,f2693dd86766b5bf8fd6ab87e2e970d564d20aff@54.193.250.204:26656,f878d40c538c8c23653a5b70f615f8dccec6fb9f@54.215.187.94:26656, 41143f378016a05e1fa4fa8aa028035a82761b48@154.12.235.67:46656" && \
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.0gchain/config/config.toml
```
---------------------------------------------

```
EXTERNAL_IP=$(wget -qO- eth0.me) \
PROXY_APP_PORT=26658 \
P2P_PORT=26656 \
PPROF_PORT=6060 \
API_PORT=1317 \
GRPC_PORT=9090 \
GRPC_WEB_PORT=9091
```

---------------------------------------------

```
sed -i \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$PROXY_APP_PORT\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$RPC_PORT\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$PPROF_PORT\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$P2P_PORT\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/}" \
    $HOME/.0gchain/config/config.toml 
```

---------------------------------------------
```
sed -i \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$API_PORT\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_PORT\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_WEB_PORT\4/}" $HOME/.0gchain/config/app.toml   
```
---------------------------------------------
```
sed -i.bak -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.0gchain/config/app.toml
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.0gchain/config/app.toml
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" $HOME/.0gchain/config/app.toml
```
```
sed -i "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ua0gi\"/" $HOME/.0gchain/config/app.toml
```
```
sed -i "s/^indexer *=.*/indexer = \"kv\"/" $HOME/.0gchain/config/config.toml
```
```
sed -i -e 's/address = "127.0.0.1:8545"/address = "0.0.0.0:8545"/' $HOME/.0gchain/config/app.toml
```
---------------------------------------------

```

sudo tee /etc/systemd/system/ogd.service > /dev/null <<EOF
[Unit]
Description=OG Node
After=network.target

[Service]
User=root
Type=simple
ExecStart=$(which 0gchaind) start --json-rpc.api eth,txpool,personal,net,debug,web3 --home $HOME/.0gchain
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.0gchain"
Environment="DAEMON_NAME=0gchaind"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.0gchain/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
اینتر بزنید


---------------------------------------------
```
sudo systemctl daemon-reload
```
```
sudo systemctl enable ogd 
```   
```
sudo systemctl restart ogd  
```  


```
curl -o $HOME/update_0g_peers.sh https://zerog.snapshot.nodebrand.xyz/update_0g_peers.sh
chmod +x $HOME/update_0g_peers.sh
$HOME/update_0g_peers.sh
```
```
curl -o $HOME/.0gchain/config/addrbook.json https://zerog.snapshot.nodebrand.xyz/addrbook.json

```
```
sudo systemctl stop ogd
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain --keep-addr-book
curl https://snapshots-testnet.nodejumper.io/0g-testnet/0g-testnet_latest.tar.lz4 | sudo lz4 -dc - | sudo tar -xf - -C $HOME/.0gchain
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
sudo systemctl restart ogd && sudo journalctl -u ogd -f -o cat
```
اینجا کنترل سی بزنید استوپ شه

----------------------------------------------------------------
```
0gchaind status | jq '{ latest_block_height: .sync_info.latest_block_height, catching_up: .sync_info.catching_up }'
```
صبر کنید این بخش به حالت 
false
دربیاد بعد میشه رفت مرحله بعد

https://dashboard.nodebrand.xyz/0G/block

از سایت بالا هم بلاک شبکه رو چک کنید که بلاک ها هم سینک شه

بعد از این که خروجی فالس شد

باید ولت بسازید

دقت کنید چون اینجا به من کلید خصوصی میده من مجبورم اینجارو برم جلو



----------------------------------------------------------------

new wallet :
```
0gchaind keys add $WALLET_NAME --eth
```
اینجا ولت جدید میسازه کلید خصوصی یاد داشت کنید
پسسورد بذارید رو ولت . حواستون باشه شما تو ترمینال وقتی پسوورد رو تایپ میکنید چیزی نمیبینید
هم موقع گذاشتنش هم موقع وارد کردنش 
پس درست وارد کنید

```
0gchaind keys unsafe-export-eth-key $WALLET_NAME
```
اینجا پسسورد بزنید به شما یه کلید خصوصی میده که متا مسک ولت رو ایمپورت کنید با کلید خصوصی 
بعدش فاست بریزید داخلش حد اقل 2 تا 

https://faucet.0g.ai/


```
0gchaind debug addr $(0gchaind keys show $WALLET_NAME -a) | grep 'Address (hex):' | awk -F ': ' '{print "0x" $2}'
```

این ادرس ولتتون رو میده
```
0gchaind q bank balances $(0gchaind keys show $WALLET_NAME -a)
```
کامند بالا هم موجودی ولت رو میده
اگر چک کردید نود سینک بود و موجودی داشتید

--------------------------------------------------


```
0gchaind tx staking create-validator \
--amount=1000000ua0gi \
--pubkey=$(0gchaind tendermint show-validator) \
--moniker="$MONIKER" \
--chain-id=zgtendermint_16600-1 \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.01" \
--details="CRYPTON" \
--min-self-delegation="1" \
--from="$WALLET_NAME" \
--gas=auto \
--gas-adjustment=1.4 \
-y
```






-------------------------------------------------


```
0gchaind q staking validator $(0gchaind keys show wallet --bech val -a)
```
اپراتور ادرس رو هم بردرید

-----------------------------------------------------



delegate
```
0gchaind tx staking delegate $(0gchaind keys show $WALLET_NAME --bech val -a) 1000000ua0gi \
--from $WALLET_NAME \
-y
```
بعدا میتونید برای ولتتون فاست بگیرید اینجا با این کد بزنید و دلیگیت کنید فقط جای میزان رو مشخص کنید به صورت چند رقمی که تو کد های بالا بود 

برای الان نیست

بریم ببینیم نودمون راه افتاد ؟

-------------------------------------------------------



https://docs.google.com/forms/d/e/1FAIpQLScsa1lpn43F7XAydVlKK_ItLGOkuz2fBmQaZjecDn76kysQsw/viewform?ts=6617a343

2 نکته برای پر کردن فرم
```
0gchaind keys show wallet --bech val -a
```
این کد رو بزنید ادرس ولیدیتورتونو بگیرید 

و قسمت پورت بعد 2 نقطه

26657


رو بزنید

مثلا 123.323.123.232:26657



----------------------------------------------------------


اخرش هم فرم رو پر کن

دیسکورد عضو شو

لینکشو میذارم

https://discord.com/invite/0glabs



