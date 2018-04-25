Radius-Masternode + Sentinel Installation Guide
===============================================

This Installation GUIDE will show you how to install a RADIUS Masternode on any linux VPS (Vultr for this guide) with a local windows wallet for receiving payments.

Radius Core allows controlling multiple remote masternodes from a single wallet. The wallet needs to have a valid collateral output of 1000 coins for each masternode and uses a configuration file named `masternode.conf` which can be found in the following data directory (depending on your operating system):
 * Windows: %APPDATA%\RadiusCore\
 * Mac OS: ~/Library/Application Support/RadiusCore/
 * Unix/Linux: ~/.radiuscore/

## Linux VPS installation

**A. Linux VPS Masternode Setup**

1. Shell script to run as Root to install a [Radius Masternode](http://www.radiuscrypto.online/) on a Linux server running Ubuntu 16.04. Use it on your own risk.
```
wget -q https://cdn.rawgit.com/Natizyskunk/Radius-Masternode/187c50cc/radius_install.sh
bash radius_install.sh
```
Follow the instructions as this will install all requirements and create your Masternode PirvateKey for the first time. **Don't forget to copy/backup it !!! You'll need it just after !**

2. Restarting, enabling at startup and and checking Radius:service.
```
systemctl stop Radius.service
sleep 3
systemctl start Radius.service
systemctl enable Radius.service
systemctl status Radius.service
```

**B. Windows Desktop Wallet Setup (radius-qt)**

After the Masternode is up and running, you need to configure the desktop (radius-qt) wallet accordingly. Here are the steps:  
1. Open the Radius-qt Desktop Wallet.
2. Go to RECEIVE and create a New Address: **MN1**
3. Send **1000** RADIUS to **MN1**. You need to send all 1000 coins in one single transaction.
4. Wait for 15 confirmations.
5. Go to **Help -> "Debug Window - Console"**
6. Type the following command: **masternode outputs** and copy the outputs to a notepad (will be used at step 8).
7. Go to  **Tools -> "Open Masternode Configuration File"** (`masternode.conf`).
8. Add the following entry:
```
Alias Address Privkey TxHash TxIndex
```
* Alias: **MN1**
* Address: **VPS_IP:PORT**
* Privkey: **Masternode Private Key**
* TxHash: **First value from Step 6**
* TxIndex:  **Second value from Step 6**
`masternode.conf` is a space separated text file. Each line consists of an alias, IP address followed by port, masternode private key, collateral output transaction id and collateral output index.

Example:
```
MN1 143.80.83.41:4090 93HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg 7603c20a05258c208b58b0a0d77603b9fc93d47cfa403035f87f3ce0af814566 0
MN2 143.80.83.42:4090 92Da1aYg6sbenP6uwskJgEY2XWB5LwJ7bXRqc3UPeShtHWJDjDv 5d898e78244f3206e0105f421cdb071d95d111a51cd88eb5511fc0dbf4bfd95f 1
```
In the example above:
* the collateral of 1000 RADIUS for `mn1` is output `0` of transaction [7603c20a05258c208b58b0a0d77603b9fc93d47cfa403035f87f3ce0af814566](https://test.explorer.radius.org/tx/7603c20a05258c208b58b0a0d77603b9fc93d47cfa403035f87f3ce0af814566)
* the collateral of 1000 RADIUS for `mn2` is output `1` of transaction [5d898e78244f3206e0105f421cdb071d95d111a51cd88eb5511fc0dbf4bfd95f](https://test.explorer.radius.org/tx/5d898e78244f3206e0105f421cdb071d95d111a51cd88eb5511fc0dbf4bfd95f)
9. Save and close the file.
10. Go to **Masternode Tab**. If you tab is not shown, please enable it from: **Settings - Options - Wallet - Show Masternodes Tab**
11. Click **Update status** to see your node. If it is not shown, close the wallet and start it again. Make sure the wallet is un
12. Select your MN and click **Start Alias** to start it.
13. Alternatively, open **Debug Console** and type:
```
masternode start-alias MN1
``` 
14. Login to your VPS and check your masternode status by running the following command. 
```
radius-cli masternode status
```
**C. Usage**:
```
radius-cli masternode status
radius-cli mnsync status
radius-cli getinfo
```
Also, if you want to check/start/stop **Radius**, run one of the following commands as **root**:
```
systemctl status Radius #To check if Radius service is running  
systemctl start Radius #To start Radius service  
systemctl stop Radius #To stop Radius service  
systemctl is-enabled Radius #To check if Radius service is enabled on boot  
```

## MASTERNODE WATCHDOG FIX - LINUX VPS

**A. Sentinel Setup**

1.Donwload and installation of Sentinel-linux-x64.
```  
cd .radiuscore
wget https://github.com/ZonnCash/sentinel/releases/download/v1.1.0-win64/sentinel-lin64
chmod +x sentinel-lin64
cp radius.conf radiusmn.conf
echo "desire_conf=/root/.radiuscore/radiusmn.conf" > sentinel.conf
```  
2.Starting and runing Sentinel to fix "Watchdog expired" error.
```
nohup ./sentinel-lin64 &
``` 
This command will run Sentinel in the background without stopping the service so you can close your ssh connection without any problems, Sentinel will still run.

**B. Checking Sentinel Process status**
1. go back to your root folder type in `top`. 
This will give a list of all your services that are running on your server. This way you can check if Sentinel-lin64 is effectively in the process list.
2. If you want to kill the process, look for it PID number. Copy it and then run ` kill -9 nPID` or `killall -9 sentinel-lin64`. This should stop the process. !!! `nPID` is the Process ID to kill.
!!! Now to wait round 30min or longer and you will see your masternode status going to "ENABLED" instead of "WATCHDOG EXPIRE". !!!

## Contact + Infos

Explorer: http://explorer.radiuscrypto.online <BR>
Github 1: https://github.com/Natizyskunk/Radius-qt/ <BR>
Github 2: https://github.com/RadiusCrypto/Radius <BR>
Discord: https://discord.gg/eWjGrKk

## Donations

Any donation is highly appreciated

**RADIUS**: <BR>
**BTC**: <BR>
**ETH**: <BR>
**LTC**:<BR>
**XMR**: <BR>
**ZEC**: <BR>
**ETN**: <BR>
**RVN**: 
