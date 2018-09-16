stashd-rpc.js
===============

[![NPM Package](https://img.shields.io/npm/v/stashd-rpc.svg?style=flat-square)](https://www.npmjs.org/package/@stashcore/stashd-rpc)
[![Build Status](https://img.shields.io/travis/stashpayio/stashd-rpc.svg?branch=master&style=flat-square)](https://travis-ci.org/stashpayio/stashd-rpc)
[![Coverage Status](https://img.shields.io/coveralls/stashpayio/stashd-rpc.svg?style=flat-square)](https://coveralls.io/r/stashpayio/stashd-rpc?branch=master)

A client library to connect to Stash Core RPC in JavaScript.

## Get Started

stashd-rpc.js runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install @stashcore/stashd-rpc
```

## RpcClient

Config parameters : 

	- protocol : (string - optional) - (default: 'https') - Set the protocol to be used. Either `http` or `https`.
	- user : (string - optional) - (default: 'user') - Set the user credential.
	- pass : (string - optional) - (default: 'pass') - Set the password credential.
	- host : (string - optional) - (default: '127.0.0.1') - The host you want to connect with.
	- port : (integer - optional) - (default: 9998) - Set the port on which perform the RPC command.

Promise vs callback based

  - `require('bitcoind-rpc-stash/promise')` to have promises returned
  - `require('bitcoind-rpc-stash')` to have callback functions returned
	
## Examples

Config:
```javascript
var config = {
    protocol: 'http',
    user: 'stash',
    pass: 'local321',
    host: '127.0.0.1',
    port: 19998
};
```

Promise based:
```javascript
var RpcClient = require('bitcoind-rpc-stash/promise');
var rpc = new RpcClient(config);

rpc.getRawMemPool()
    .then(ret => {
        return Promise.all(ret.result.map(r => rpc.getRawTransaction(r)))
    })
    .then(rawTxs => {
        rawTxs.forEach(rawTx => {
            console.log(`RawTX: ${rawTx.result}`);
        })
    })
    .catch(err => {
        console.log(err)
    })

```

Callback based (legacy):
```javascript
var run = function() {
  var bitcore = require('bitcore');
  var RpcClient = require('bitcoind-rpc-stash');
  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

## Help 

You can dynamically access to the help of each method by doing
```
const RpcClient = require('bitcoind-rpc-stash');
var client = new RPCclient({
    protocol:'http',
    user: 'stash',
    pass: 'local321', 
    host: '127.0.0.1', 
    port: 19998
});

var cb = function (err, data) {
    console.log(data)
};
client.help(cb); //Get full help
client.help('getinfo',cb); //Get help of specific method
```
## License

**Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).**

Copyright 2013-2014 BitPay, Inc.
