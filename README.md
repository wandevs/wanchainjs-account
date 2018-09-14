# SYNOPSIS

[![CircleCI][circle-image]][circle-url]
[![Coverage Status][coveralls-image]][coveralls-url]
[![dependency status][dep-image]][dep-url]
[![NPM][npm-image]][npm-url]

[circle-image]: https://circleci.com/gh/WanJS/wanchainjs-account.svg?style=svg
[circle-url]: https://circleci.com/gh/WanJS/wanchainjs-account
[dep-image]: https://david-dm.org/WanJS/wanchainjs-account.svg
[dep-url]: https://david-dm.org/WanJS/wanchainjs-account
[coveralls-image]: https://coveralls.io/repos/github/WanJS/wanchainjs-account/badge.svg?branch=dev
[coveralls-url]: https://coveralls.io/github/WanJS/wanchainjs-account?branch=dev
[npm-image]: http://img.shields.io/npm/v/wanchainjs-account.svg
[npm-url]: https://www.npmjs.org/package/wanchainjs-account

This library eases the handling of Wanchain accounts, where accounts can be either external accounts 
or contracts (see 
[Account Types](http://ethdocs.org/en/latest/contracts-and-transactions/account-types-gas-and-transactions.html) docs).

Note that the library is not meant to be used to handle your wallet accounts, use e.g. the
[web3-eth-personal](http://web3js.readthedocs.io/en/1.0/web3-eth-personal.html) package from the 
``web3.js`` library for that. This is just a semantic wrapper to ease the use of account data and
provide functionality for reading and writing accounts from and to the Ethereum state trie.

# INSTALL
`npm install wanchainjs-account`

# BROWSER
This module work with `browserify`.

# API
 - [`new Account([data])`](#new-accountdata)
  - [`Account` Properties](#account-properties)
  - [`Account` Methods](#account-methods)
    - [`account.isEmpty()`](#accountisempty)
    - [`account.isContract()`](#accountiscontract)
    - [`account.serialize()`](#accountserialize)
    - [`account.toJSON()`](#accounttojson)
    - [`account.getCode(trie, cb)`](#accountgetcodetrie-cb)
    - [`account.setCode(trie, code, cb)`](#accountsetcodetrie-code-cb)
    - [`account.getStorage(trie, key, cb)`](#accountgetstoragetrie-key-cb)
    - [`account.setStorage(trie, key, val, cb)`](#accountsetstoragetrie-key-val-cb)

### `new Account([data])`
Creates a new account object
- `data` - an account can be initialized with either a `buffer` containing the RLP serialized account.
 Or an `Array` of buffers relating to each of the account Properties, listed in order below.  For example:
```javascript
var raw = [ 
  '0x02', //nonce
  '0x0384', //balance
  '0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421', //stateRoot
  '0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470'  //codeHash
  ];

var account = new Account(raw);
```

Or lastly an `Object` containing the Properties of the account:

```javascript
var raw = {
  nonce: '',
  balance: '0x03e7',
  stateRoot: '0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421',
  codeHash: '0xc5d2460186f7233c927e7db2dcc703c0e500b653ca82273b7bfad8045d85a470'
};

var account = new Account(raw);
```
For `Object` and `Array` each of the elements can either be a `Buffer`, hex `String`, `Number`, or an object with a `toBuffer` method such as `Bignum`.

### `Account` Properties
- `nonce` - The account's nonce.
- `balance`  - The account's balance in wei.
- `stateRoot` - The stateRoot for the storage of the contract.
- `codeHash` - The hash of the code of the contract.

### `Account` Methods

#### `account.isEmpty()`
Returns a `Boolean` determining if the account is empty.

#### `account.isContract()`
Returns a `Boolean` deteremining if the account is a contract.

#### `account.serialize()`
Returns the RLP serialization of the account as a `Buffer`.

#### `account.toJSON([object])`
Returns the account as JSON.
- `object` - A `Boolean` that defaults to false. If `object` is true then this will return an `Object`, else it will return an `Array`.

#### `account.getCode(trie, cb)`
Fetches the code from the trie.
- `trie` - The [trie](https://github.com/ethereumjs/merkle-patricia-tree) storing the accounts.
- `cb` - The callback.

#### `account.setCode(trie, code, cb)`
Stores the code in the trie.
- `trie` - The [trie](https://github.com/ethereumjs/merkle-patricia-tree) storing the accounts.
- `code` - A `Buffer`.
- `cb` - The callback.

Example for ``getCode`` and ``setCode``:
```javascript
// Requires manual merkle-patricia-tree install
const SecureTrie = require('merkle-patricia-tree/secure')
const Account = require('./index.js')

let code = Buffer.from('73095e7baea6a6c7c4c2dfeb977efac326af552d873173095e7baea6a6c7c4c2dfeb977efac326af552d873157', 'hex')

let raw = {
  nonce: '',
  balance: '0x03e7',
  stateRoot: '0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421',
  codeHash: '0xb30fb32201fe0486606ad451e1a61e2ae1748343cd3d411ed992ffcc0774edd4'
}

let account = new Account(raw)
let trie = new SecureTrie()

account.setCode(trie, code, function (err, codeHash) {
  console.log(`Code with hash 0x${codeHash.toString('hex')} set to trie`)
  account.getCode(trie, function (err, code) {
    console.log(`Code ${code.toString('hex')} read from trie`)
  })
})
```

#### `account.getStorage(trie, key, cb)`
Fetches `key` from the account's storage.

#### `account.setStorage(trie, key, val, cb)`
Stores a `val` at the `key` in the contract's storage.

Example for ``getStorage`` and ``setStorage``:

```javascript
// Requires manual merkle-patricia-tree install
const SecureTrie = require('merkle-patricia-tree/secure')
const Account = require('./index.js')

let raw = {
  nonce: '',
  balance: '0x03e7',
  stateRoot: '0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421',
  codeHash: '0xb30fb32201fe0486606ad451e1a61e2ae1748343cd3d411ed992ffcc0774edd4'
}

let account = new Account(raw)
let trie = new SecureTrie()

let key = Buffer.from('0000000000000000000000000000000000000000', 'hex')
let value = Buffer.from('01', 'hex')

account.setStorage(trie, key, value, function (err, value) {
  account.getStorage(trie, key, function (err, value) {
    console.log(`Value ${value.toString('hex')} set and retrieved from trie.`)
  })
})
```
