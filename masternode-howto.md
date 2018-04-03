# Radius Masternode Setup Guide

This guide will show you how to setup and enable a Radius Masternode. For simplicity, we will assume the local wallet is installed already. Should you need to compile a local wallet for Linux or OSX, refer to the instructions given under 'Setting up the Masternode Server'

## Requirements

+ 1000 Radius
+ Your local ("every day") wallet, can be Windows, Linux.
+ Your Masternode VPS or dedicated server.
⋅⋅+ It needs to have a public, static IPv4 address.
⋅⋅+ Port 4090/tcp must be open to the internet
⋅⋅+ You can only host one MN per IPv4 address.

## Instructions

### Generate the Private Key for the MN
This has to be done on your local wallet. Copy the result to a safe place. Do not lose it. We will use it later on.

**Debug console in GUI Wallet, probably Windows**
Enter the below command into the debug console of your GUI wallet to create your MN private key"

```
masternode genkey
```

**CLI-Interface on Linux, OSX**
Use your Terminal to enter this command: 

```
# radius-cli masternode genkey
```

The result will look something like this: 

```
WV0wX2hjrVVuPPxhVVVZ2DPB3tJ0x03deadbeefc0ff33EvxKY
```

### Collateral transaction
**NOTE __it is ultra important that you pay good attention here. We cannot help you if you send the collateral to the wrong address. Double and triple check everything before you send the collateral transaction__**

**Windows or GUI Wallet**
+ Create a new address
+ Send 1000 RADIUS to that address (make sure it is the right one!)
+ In the GUI debug console, enter this command:

```
masternode outputs
```

**CLI-Interface on Linux, OSX***

```
# radius-cli getnewaddress
RUxvG3Ya7jboRHbg1XGe4MWXPjJoVHLUBz
# radius-cli sendtoaddress RUxvG3Ya7jboRHbg1XGe4MWXPjJoVHLUBz 1000
51a26de5f655a81b730463e236aefef24152b053df0f5453099032cf471743bc
# radius-cli masternode outputs
```

The result will look like this: 

```
{
  "04e91d19e6e6cfd5c73888d72d43467893267821fb3244a65ef0bed102d2166e": "1"
}
```


### Setting up the Masternode Server

**NOTE:** These instructions are Ubuntu-specific, but should be easily adaptable to other Linux distributions.

```# sudo apt-get -y update && sudo apt-get -y upgrade && sudo apt-get -y dist-upgrade
[lots of output here]
```

```
# apt-get -y install git automake build-essential libtool autotools-dev autoconf pkg-config libssl-dev libboost-all-dev software-properties-common
```

```
# add-apt-repository ppa:bitcoin/bitcoin
<press enter>
```

```
# apt-get update
[lots of output here]
# apt-get install libdb4.8-dev libdb4.8++-dev libminiupnpc-dev libevent-dev
[more output here]
```

Create a new user, all work from now on will be done in this user account.

```
# useradd -m -d /home/radius -s /bin/bash radius
# passwd radius
<follow instructions given>
# su - radius
```

You are now logged in as the user `radius`. Now, clone the Radius GIT repository and compile the wallet:

```
# git clone https://github.com/RadiusCrypto/Radius.git
[a lot of output]
# cd Radius
# ./autogen.sh
# ./configure --disable-tests --disable-gui-tests --disable-bench --disable-zmq --enable-experimental-asm --with-gui=no
# make
```

Now, do a 'manual install' of the compiled binaries
```
# mkdir ~/bin
# cd ~/Radius
# cp src/radiusd src/radius-cli src/radius-tx ~/bin
# strip ~/bin/radius*
# mkdir ~/.radiuscore
# touch ~/.radiuscore/radius.conf
```

We will have to adapt the Radius configuration file. 

```
# nano ~/.radiuscore/radius.conf
```

Put in the values below, adapt the rpcuser, rpcpassword, masternodeprivkey and externalip lines. rpcuser and rpcpassword can be chosen freely, having a superduper complex password is not overly important here. Set masternodeprivkey= to the key we have generated before.

```
rpcuser=radius-masternode-howto
rpcpassword=password-for-your-masternode
rpcallowip=127.0.0.1
listen=1
server=1
daemon=1
maxconnections=256
masternode=1
logtimestamps=1
masternodeprivkey=WV0wX2hjrVVuPPxhVVVZ2DPB3tJ0x03deadbeefc0ff33EvxKY
externalip=1.2.3.4:4090
addnode=107.173.125.64
addnode=107.173.181.205
addnode=199.247.21.30
addnode=199.247.10.115
```

Hit Ctrl+x, then select save.

Now edit your masternode.conf at your windows wallet

alias IP:4090 masternodeprivkey collateral_output_txid collateral_output_index

Restart windows wallet

Go to your vps and type ‘’./radiusd’’

Now start your masternode at your windows wallet

You set up your masternode

