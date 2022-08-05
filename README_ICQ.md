# Set up ICQ relay
# task 9 Stride Labs testnet
<br>

Test ICQ relayer between two cosmos chains

## Update system
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install wget git make htop unzip -y
```
## Install GO 1.18.3 | ONE COMMAND
```
cd $HOME && \
ver="1.18.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
## Make relayer home dir
```
cd $HOME
mkdir -p $HOME/.icq/config
```
## Download go-v2 relayer
```
git clone https://github.com/Stride-Labs/interchain-queries.git
cd interchain-queries && git checkout v2.0.0
make install
 
```
```

RUN cp -r interchain-queries/* .
RUN rm -rf interchain-queries
```
## Make you own config for Relayer v2
```
cd $HOME
mkdir -p $HOME/.icq/config
     
MEMO='Mad as a hatter#4581'
     
KEY1='wallet'
KEY2='wallet'
     
IP1='127.0.0.1'
PORT1='23657'

IP2='157.90.149.6'
PORT2='16657'
```
# NEXT - COPY ALL ONE COMAND TO TERMINAL
```

sudo tee $HOME/.icq/config/config.yaml > /dev/null <<EOF
default_chain: stride
chains:
  stride:
    key: icq1
    chain-id: STRIDE_CHAIN_ID
    rpc-addr: http://STRIDE_ENDPOINT:26657
    grpc-addr: http://STRIDE_ENDPOINT:9090
    account-prefix: stride
    keyring-backend: test
    gas-adjustment: 1.2
    gas-prices: 1ustrd
    key-directory: /icq/keys
    debug: false
    timeout: 20s
    block-timeout: ""
    output-format: json
    sign-mode: direct
  gaia:
    key: icq2
    chain-id: GAIA
    rpc-addr: http://GAIA_ENDPOINT:26657
    grpc-addr: http://GAIA_ENDPOINT:9090
    account-prefix: cosmos
    keyring-backend: test
    gas-adjustment: 1.2
    gas-prices: 1uatom
    key-directory: /icq/keys
    debug: false
    timeout: 20s
    block-timeout: ""
    output-format: json
    sign-mode: direct
EOF
```

## Import  keys for the relayer to use when signing and relaying transactions
   ```
echo "\nAdd ICQ relayer addresses for Stride and Gaia:"
# TODO(TEST-82) redefine stride-testnet in lens' config to $STRIDE_CHAIN and gaia-testnet to $main-gaia-chain, then replace those below with $STRIDE_CHAIN and $GAIA_CHAIN
$ICQ_RUN keys restore test "$ICQ_STRIDE_KEY" --chain stride-testnet
$ICQ_RUN keys restore test "$ICQ_GAIA_KEY" --chain gaia-testnet
```

## Create go-v2 relayer service file
 (copy and paste into the terminal with one command)
```
sudo tee /etc/systemd/system/icqd.service > /dev/null <<EOF
[Unit]
Description=Relayer_v2
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which rly) start task --log-format logfmt --processor events
Restart=on-failure
RestartSec=10
LimitNOFILE=4096
[Install]
WantedBy=multi-user.target
EOF
```

## Start service
```
sudo systemctl daemon-reload
sudo systemctl enable icqd
sudo systemctl restart icqd && journalctl -u icqd -f
```




# ????
RUN mkdir stride_ref
RUN mkdir stride_ref/stride
COPY . /src/app/stride_ref/stride
WORKDIR /src/app/stride_ref/stride
WORKDIR /src/app
RUN go mod download
RUN go build

RUN ln -s /src/app/interchain-queries /usr/local/bin
RUN adduser --system --home /icq --disabled-password --disabled-login icq -U 1000
USER icq
