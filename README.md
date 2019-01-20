# obsidian-faucet
This creates an API so that the faucet can make transactions.

It connects to a node running a high availability wallet. To create a high availability wallet (ha) in a node, please fork the walletd-ha repository and see the instructions within how to run obscure-service as a daemon.

This API makes rpc-calls using a turtle-service RPC wrapper written in JS. Instructions to set up your own API to make calls to a node is shown below:


Clone this repository

git clone https://github.com/ObscureIM/obscure-faucet-api.git
cd obscure-faucet-api
npm install --unsafe-perm
node init.js 
