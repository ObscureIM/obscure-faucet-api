# obsidian-faucet
This creates an API so that the faucet can make transactions.

It connects to a node running a high availability wallet. To create a high availability wallet (ha) in a node, please fork the walletd-ha repository and see the instructions within how to run obscure-service as a daemon.

This API makes rpc-calls using a turtle-service RPC wrapper written in JS. Instructions to set up your own API to make calls to a node is shown below:


Clone this repository
```
git clone https://github.com/ObscureIM/obscure-faucet-api.git
cd obscure-faucet-api
npm install --unsafe-perm
cd lib
```
Edit the config.json file located in lib

```
var express = require("express");

var app = express();
var allowCrossDomain = function(req, res, next) {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Methods', 'GET,PUT,POST,DELETE,OPTIONS');
    res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization, Content-Length, X-Requested-With');

    // intercept OPTIONS method
    if ('OPTIONS' == req.method) {
      res.send(200);
    }
    else {
      next();
    }
};
app.use(allowCrossDomain)
//Initialize the app
var server = app.listen(process.env.PORT || 8080, function () {
    var port = server.address().port;
    console.log("App now running on port", port);
  })


const TurtleService = require('turtlecoin-rpc').TurtleService

const service = new TurtleService({
  host: '111.111.111', // ip address or hostname of the turtle-service host
  port: 8070, // what port is turtle-service running on
  timeout: 2000, // request timeout
  ssl: false, // whether we need to connect using SSL/TLS
  rpcPassword: 'password', // must be set to the password used to run turtle-servic
  // RPC API default values
  defaultMixin: false, // the default mixin to use for transactions, the default setting is false which means we don't have a default value
  defaultFee: 0.1, // the default transaction fee for transactions
  defaultBlockCount: 1, // the default number of blocks when blockCount is required
  decimalDivisor: 100000000, // Currency has many decimal places?
  defaultFirstBlockIndex: 1, // the default first block index we will use when it is required
  defaultUnlockTime: 0, // the default unlockTime for transactions
  defaultFusionThreshold: 10000000, // the default fusionThreshold for fusion transactions
})

// CONTACTS API ROUTES BELOW

// Generic error handler used by all endpoints.
function handleError(res, reason, message, code) {
  console.log("ERROR: " + reason);
  res.status(code || 500).json({"error": message});
}

//first lets get the balance of the faucet
app.get('/api/balance' , function(req,res) {
    service.getBalance({
      address: 'XSC1FEMLHtPb91W8Gk9AbpNtn8z96J2pxf7Mg68VFf3bgPAq8MTCXr71odVyNV7WqSiQtDnxRhtBZ4kyGTUnFJai1whiR8bbPs'
    }).then((result) => {
      res.send(result)
    }).catch(function(error) {
      res.send(error)
    })
})

//trigger a call to an address
app.get('/api/sendTo',function(req,res) {
  //make sure to call to /api/sendTo?address=
  var address = req.query.address
  service.sendTransaction({
    transfers:[
      //send 1 XSC to the address
      service.newTransfer(address,1)
    ],
    fee: 0.1,
    mixin:3,
  }).then((result) => {
    res.send(result)
  }).catch(function(error) {
    res.send(error)
  })
})
```

