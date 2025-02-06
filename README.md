# Devscriptions

A minter and protocol for inscriptions on DogecoinEV. 

## ⚠️⚠️⚠️ Important ⚠️⚠️⚠️

Use this wallet for inscribing only! Avoid storing devscriptions in DogecoinEV core.

Please use a fresh paper wallet to mint to with nothing else in it until proper wallet for devscriptions support comes.. 

This wallet is not meant for storing funds or inscriptions.

## Prerequisites

To use this, you'll need to setup a DogecoinEV node, clone this repo and install Node.js on your computer.

### Setup DogecoinEV node

Install DogecoinEV Core from the official DogecoinEV github: (https://github.com/justanyuser/dogecoinEV/releases)

### ⚠️⚠️⚠️ Important ⚠️⚠️⚠️
A configuration file needs to be created before you continue with the sync.

-Stop your node

-Create a `dogecoinev.conf` file in your DogecoinEV data folder.

-Copy and paste this to the created file. Set your own username/password. Save it!

```
rpcuser=
rpcpassword=
rpcport=42069
server=1
listen=1
txindex=1
rpcallowip=127.0.0.1
```

-Start your node again

-Wait for your node to sync with the network.

==========

### Install NodeJS

Please head over to (https://nodejs.org/en/download) and follow the installation instructions for your system.

==========

### Setup Devscriptions

#### Clone Devscription minter
On your Terminal, type the following commands:
```
cd
git clone https://github.com/justanyuser/Devscriptions.git
```
#### Setup minter

```
cd Devscriptions
npm install
cd bitcore-lib-dev
npm install
cd ..
``` 

After all dependencies are solved, you can configure the environment:

#### Configure environment

Create a `.env` file with your node information. Set the same username/password used in `dogecoinev.conf`.

```
NODE_RPC_URL=http://127.0.0.1:42069
NODE_RPC_USER=
NODE_RPC_PASS=
TESTNET=false
FEE_PER_KB=50000000
```

==========

#### ⚠️⚠️⚠️ Important ⚠️⚠️⚠️
Before proceeding, please make sure your node is fully synced.
Have fun!

### Managing wallet balance

Generate a new `.wallet.json` file:

```
node . wallet new
```

Retrieve your private key from `.wallet.json` and import it in DogecoinEV Core, this can be done from the GUI or the following command

```
dogecoinev-cli importprivkey <your_private_key> <optional_label> false
```

Then send DOGE to the address displayed. Once sent, sync your wallet:

```
node . wallet sync
```

If you are minting a lot, you can split up your UTXOs:

```
node . wallet split <count>
```

When you are done minting, send the funds back:

```
node . wallet send <address> <optional amount>
```

==========

### Minting Devscriptions

**Note**: Please use a fresh wallet to mint to with nothing else in it until proper wallet for devscriptions support comes. 

**Do not mint to DogecoinEV Core**

#### Inscribe a file
From file:

```
node . mint <address> <path>
```

From data:

```
node . mint <address> <content type> <hex data>
```

Examples:

```
node . mint DdB1ocks3ozcti7m5a3i2wViSuFAchLm3n dev.jpeg
```

```
node . mint DdB1ocks3ozcti7m5a3i2wViSuFAchLm3n "text/plain;charset=utf-8" 52696262697421 
```

#### Deploy DEV-20

```
node . dev-20 deploy <address> <ticker> <max token supply> <max allowed mint limit>
```

Examples: 

```
node . dev-20 deploy DdB1ocks3ozcti7m5a3i2wViSuFAchLm3n doge 1000 100
```

#### Mint DEV-20

```
node . dev-20 mint <address> <ticker> <amount>
```

Examples: 

```
node . dev-20 mint DdB1ocks3ozcti7m5a3i2wViSuFAchLm3n doge 100
```

### Viewing Devscriptions

**Note**: There is currently a bug preventing preview of some larger devscription files. Wait for a fix or a devscription indexer. 

Viewing small inscriptions seems to work. Investigating...

Start the server:

```
node . server
```

And open your browser to:

```
http://localhost:3000/tx/4650300f65470c359c070ae6b88ab7945adad68458c33285968ce0bfa502e52c
```

==========

### Additional Info

#### Protocol

The devscriptions protocol allows any size data to be inscribed onto subwoofers.

An inscription is defined as a series of push datas:

```
"ord"
OP_1
"text/plain;charset=utf-8"
OP_0
"Ribbit!"
```

For devscriptions, we introduce a couple extensions. First, content may spread across multiple parts:

```
"ord"
OP_2
"text/plain;charset=utf-8"
OP_1
"Ribbit and "
OP_0
"ribbit ribbit!"
```

This content here would be concatenated as "Ribbit and ribbit ribbit!". This allows up to ~1500 bytes of data per transaction.

Second, P2SH is used to encode inscriptions.

There are no restrictions on what P2SH scripts may do as long as the redeem scripts start with inscription push datas.

And third, inscriptions are allowed to chain across transactions:

Transaction 1:

```
"ord"
OP_2
"text/plain;charset=utf-8"
OP_1
"Ribbit and "
```

Transaction 2

```
OP_0
"ribbit ribbit!"
```

With the restriction that each inscription part after the first must start with a number separator, and number separators must count down to 0.

This allows indexers to know how much data remains.


### Troubleshooting

#### I'm getting ECONNREFUSED errors when minting

There's a problem with the node connection. Your `dogecoinev.conf` file should look something like:

```
rpcuser=
rpcpassword=
rpcport=42069
server=1
listen=1
txindex=1
rpcallowip=127.0.0.1
```

Make sure `port` is not set to the same number as `rpcport`. Also make sure `rpcauth` is not set.

Your `.env file` should look like:

```
NODE_RPC_URL=http://127.0.0.1:42069
NODE_RPC_USER=
NODE_RPC_PASS=
TESTNET=false
FEE_PER_KB=50000000
```

#### Other issues

Try restarting your DogecoinEV node.

If still stuck, ask ChatGPT or search online for other solutions.
