# Radius Masternode Setup Guide

This guide will show you how to setup and enable a Radius Masternode. For simplicity, we will assume the local wallet is installed already. Should you need to compile a local wallet for Linux or OSX, refer to the instructions given under 'Setting up the Masternode Server'

## Requirements

+ 1000 Radius
+ Your local ("every day") wallet, can be Windows, Linux.
+ Your Masternode VPS or dedicated server.
	+ It needs to have a public, static IPv4 address.
	+ Port 4090/tcp must be open to the internet
	+ You can only host one MN per IPv4 address.

## Instructions

### Generate the Private Key for the MN
This has to be done on your local wallet. Copy the result to a safe place. Do not lose it. We will use it later on.

#### Debug console in GUI Wallet, probably Windows
Enter the below command into the debug console of your GUI wallet to create your MN private key"

```
masternode genkey
```

#### CLI-Interface on Linux, OSX
Use your Terminal to enter this command: 

```
# radius-cli masternode genkey
```

The result will look something like this: 

```
WV0wX2hjrVVuPPxhVVVZ2DPB3tJ0x03deadbeefc0ff33EvxKY
```

### Collateral transaction
**NOTE __it is ultra important that you pay good attention here. We cannot help you if you send the collateral to the wrong address. Double and triple check everything before you send the collateral transaction!!!__**

#### Windows or GUI Wallet
+ Create a new address
+ Send 1000 RADIUS to that address (make sure it is the right one!)
+ In the GUI debug console, enter this command:

```
masternode outputs
```

#### CLI-Interface on Linux, OSX

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

The first long hex string is the `collateral_output_txid`, the number after is the `collateral_output_index`. We'll refer to that later in the guide.


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

Put in the values below, adapt the rpcuser, rpcpassword, masternodeprivkey and externalip lines. rpcuser and rpcpassword can be chosen freely, having a superduper complex password is not overly important here, as that part of the server will only be available when connecting from the host it is running on (via loopback). Set masternodeprivkey= to the key we have generated before.

```
txindex=1
rpcuser=radius-masternode-howto
rpcpassword=password-for-your-masternode
rpcallowip=127.0.0.1
listen=1
server=1
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

Hit Ctrl+x, then select save. Start the MN Radius server: 

```
# radiusd -daemon
```

If you want to, you can watch the logfile for errors by entering this command: 

```
# tail -f ~/.radiuscore/debug.log
```

Press `Ctrl-C` to exit the log display.

### Setting up your local wallet 

In your local wallets datadir, find a file named 'masternode.conf'. Open it in an editor of your choice, and adapt the alias, IP, masternodeprivkey, collateral_output_txid and collateral_output_index fields.

+ 'alias' is just a name you give to your node.

```
alias IP:4090 masternodeprivkey collateral_output_txid collateral_output_index
```

In our example, using the data we've gathered before, it would look like this: 

```
mn1 1.2.3.4:4090 WV0wX2hjrVVuPPxhVVVZ2DPB3tJ0x03deadbeefc0ff33EvxKY 04e91d19e6e6cfd5c73888d72d43467893267821fb3244a65ef0bed102d2166e 1
```

Save and exit the editor. Restart your local wallet. 

Now, on your local wallet, start the masternode.  If you have chosen a different alias than `mn1` above, replace it accordingly in the command.

#### GUI Wallet Debug Console, Windows

```
masternode start-alias mn1
```

#### CLI-Interface, Linux or OSX

```
# radius-cli masternode start-alias mn1
```

The result should look like this: 

```
{
  "alias": "mn1",
  "result": "successful"
}
```

Verify that the masternode has indeed started successfully by executing this command against the local wallet:

#### GUI Wallet Debug Console, Windows

```
masternode status
```

#### CLI-Interface, Linux or OSX

```
# radius-cli masternode status
```

The result should look like this with different values: 

```
{
  "outpoint": "04e91d19e6e6cfd5c73888d72d43467893267821fb3244a65ef0bed102d2166e-1",
  "service": "1.2.3.4:4090",
  "payee": "RDqEkeLJetSfpLDvN6ArTnX51PAZFP9vcu",
  "status": "Masternode successfully started"
}
```

If it does not, restart the MN server wallet and your local wallet, and it should work.

