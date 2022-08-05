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
FROM golang:1.17-bullseye

RUN apt install git

WORKDIR /src/app
RUN git clone https://github.com/Stride-Labs/interchain-queries.git
RUN cp -r interchain-queries/* .
RUN rm -rf interchain-queries

# copy over stride module and install it
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
