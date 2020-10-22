## Introduction

This is a list of the darma wallet RPC calls, their inputs and outputs, and examples of each.

All methods use the same JSON RPC interface. For example:

```
curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"make_integrated_address","params":{}}' -H 'Content-Type: application/json'
```

Note: "atomic units" refer to the smallest fraction of 1 DMCH according to the darmad implementation. **1 DMCH = 1e8 atomic units.**

### Index of JSON RPC Methods:

* [getbalance](#getbalance)
* [getaddress](#getaddress)
* [getheight](#getheight)
* [transfer](#transfer)
* [transfer_split](#transfersplit)
* [get_bulk_payments](#getbulkpayments)
* [query_key](#querykey)
* [make_integrated_address](#makeintegratedaddress)
* [split_integrated_address](#splitintegratedaddress)
* [get_transfer_by_txid](#get_transfer_by_txid)
* [get_transfers](#get_transfers)
* [validate_address](#validate_address)
* [list_address](#list_address)
* [create_address](#create_address)
* [buy_share](#buy_share)
* [repo_share](#repo_share)

---

## JSON RPC Methods:

### getbalance

Return the wallet's balance.

Inputs: *None*.

Outputs:

* *balance* - unsigned int; The total balance of the current wallet in session.
* *unlocked_balance* - unsigned int; Unlocked funds are those funds that are considered safe to spend.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getbalance"}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "balance": 140000000000,
    "unlocked_balance": 50000000000
  }
}
```

### getaddress

Return the wallet's address.

Inputs: *None*.

Outputs:

* *address* - string; The standard base58 address string of the wallet in session.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getaddress"}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "address": "dTw7r5qoAQXNY9vm9NzPxUB2rTr5VZMDhSXuS7mdAd7X9fhEYLgQrBCMCJvz9zuA6WhdTMSocjCScaHWcNqH2XKi1eSpz1ARH"
  }
}
```

### getheight

Returns the wallet's current block height.

Inputs: *None*.

Outputs:

* *height* - int; The current wallet's height.
* *topo_height* - int; The current wallet's topo height.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getheight"}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "height": 994310,
    "topo_height": 998754
  }
}
```

### transfer

Send darma to a number of recipients.

Inputs:

* *destinations* - array of destinations to receive DMCH:
  * *amount* - unsigned int; Amount to send to each destination, in atomic units.
  * *address* - string; Destination public address.
* *fee* - unsigned int; Ignored, will be automatically calculated.
* *mixin* - unsigned int; Number of outpouts from the blockchain to mix with (0 means no mixing).
* *unlock_time* - unsigned int; Number of blocks before the darma can be spent (0 to not add a lock).
* *payment_id* - string; (Optional) Random 32-byte/64-character hex string to identify a transaction.
* *get_tx_key* - boolean; (Optional) Return the transaction key after sending.
* *get_tx_hex* - boolean; (Optional) Return the raw transaction data.
* *do_not_relay* - boolean; (Optional) Create the transaction without sending to the chain.

Outputs:

* *fee* - unsigned int; Transaction fee in atomic units.
* *tx_hash* - string; Transaction hash
* *tx_key* - string; Transaction secret key, only if the `get_tx_key` is set to true.
* *tx_blob* - string; Hex encoded raw transaction data, only if the `get_tx_hex` is set to true.

#### NOTE
* If `do_not_relay` is true, the transaction won't be relayed to the chain and can be relayed later by **Daemon** RPC `sendrawtransaction`.
* The transaction size in KB is he size of `tx_blob` dividing 2048, rounded up to an integer.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"transfer","params":{"destinations":[{"amount":10000000,"address":"dTZuEhNJQRXoyJAeEoBaNW56ScQaLXyyQWgxeRL9KgAUhVzkvfiELZV7fCPBuuB2CGuJiWFQjhnhhwiH1FsHYGQGaDsaBA"},{"amount":20000,"address":"dTVtwYXDbbh7hq57wwkLH36x4D6XV6Tr2P93ANnBi9qFGyYZbx8SXWPUHC9V1o7N41b4c3WJ1kubkffRfPTPfMuB8QUqFD5"}],"fee":150000000000,"get_tx_key":true}}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "tx_hash": "29d933789db5483fa63694ed2560d3829dbf0b945fdea3c42fbfa4d381e7ac22",
    "tx_key": "c228dff7aa455d770f8cf71b9d319d2055a125cfbac1ca9485aeb1a107604d0d"
  }
}
```

### transfer_split

Same as transfer, but can split into more than one tx if necessary.

Inputs:

* *destinations* - array of destinations to receive DMCH:
  * *amount* - unsigned int; Amount to send to each destination, in atomic units.
  * *address* - string; Destination public address.
* *fee* - unsigned int; Ignored, will be automatically calculated.
* *mixin* - unsigned int; Number of outpouts from the blockchain to mix with (0 means no mixing).
* *unlock_time* - unsigned int; Number of blocks before the darma can be spent (0 to not add a lock).
* *payment_id* - string; (Optional) Random 32-byte/64-character hex string to identify a transaction.
* *get_tx_key* - boolean; (Optional) Return the transaction key after sending.

Outputs:

* *tx_hash_list* - array of string; Transaction hashes

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"transfer_split","params":{"destinations":[{"amount":2000000,"address":"dT7ZuEhNJQRXoyJAeEoBaNW56ScQaLXyyQWgxeRL9KgAUhVzkvfiELZV7fCPBuuB2CGuJiWFQjhnhhwiH1FsHYGQGaDsaBA"},{"amount":3000000,"address":"dTVtwYXDbbh7hq57wwkLH36x4D6XV6Tr2P93ANnBi9qFGyYZbx8SXWPUHC9V1o7N41b4c3WJ1kubkffRfPTPfMuB8QUqFD5"}],"mixin":3,"unlock_time":0,"payment_id":"a29602cc261fecbc93c22a152a942cc791ca6112cffe201c3e809945c9da672f","get_tx_key":true}}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "tx_hash_list": ["fb39c42cb5ff231ee9e8b381bb8734fd655b6ca28a23cbd82aa9422aa870e91b"]
  }
}
```

### get_bulk_payments

Get a list of incoming payments using a given payment id, or a list of payments ids, from a given height.

Inputs:

* *payment_ids* - array of: string
 * *min_block_height* - unsigned int; The block height at which to start looking for payments.

Outputs:

* *payments* - list of:
  * *payment_id* - string
  * *tx_hash* - string
  * *amount* - unsigned int
  * *block_height* - unsigned int
  * *unlock_time* - unsigned int

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_bulk_payments","params":{"payment_ids":["4279257e0a20608e25dba8744949c9e1caff4fcdafc7d5362ecf14225f3d9030"],"min_block_height":990000}}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "payments": [{
      "amount": 10350000000000,
      "block_height": 994327,
      "payment_id": "4279257e0a20608e25dba8744949c9e1caff4fcdafc7d5362ecf14225f3d9030",
      "tx_hash": "c391089f5b1b02067acc15294e3629a463412af1f1ed0f354113dd4467e4f6c1",
      "unlock_time": 0
    }]
  }
}
```

### query_key

Return the spend or view private key.

Inputs:

* *key_type* - string; Which key to retrieve: "mnemonic" - the mnemonic seed OR "view_key" - the view key

Outputs:

* *key* - string; The view key will be hex encoded, while the mnemonic will be a string of words.

Example (Query view key):

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"query_key","params":{"key_type":"view_key"}}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "key": "7e341d..."
  }
}
```

Example (Query mnemonic key):

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"query_key","params":{"key_type":"mnemonic"}}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "key": "adapt adapt nostril ..."
  }
}
```

### make_integrated_address

Make an integrated address from the wallet address and a payment id.

Inputs:

* *payment_id* - string; hex encoded; can be empty, in which case a random payment id is generated

Outputs:

* *integrated_address* - string
* *payment_id* - string

Example (Payment ID is empty, use a random ID):

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"make_integrated_address","params":{}}' -H 'Content-Type: application/json'
```
{
	"id": 1,
	"jsonrpc": "2.0",
	"result": {
		"integrated_address": "dTiruDXg1RCPqFVZF8aEy6HGJ5G4B1AuW98SofzynZxeWondBpJtzYMGJPb13mAAsDhocLfZxBjFA5K5CTqVkgDpGc4qgMizgdB64xBovXArW",
		"payment_id": "e585429c6685bd52"
	}
}
```

### validate_address

Validate an address.

Inputs:

* *address* - string; The address string.

Outputs:

* *is_valid* - bool; whether the address is valid.
* *is_subaddress* - bool; whether the address is subaddress.
* *is_mainnet* - bool; whether the address is mainnet address.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:13805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"validate_address","params":{"address":"dST1Z1aETPDXd3dRNpjGqZWnJMutdnKP6iPHuCFEcXTzVAdxh3WPUa2cptfByMQvFc7cqasYLotbmYNNs6Tgy4xL4hZ1pPep4U"}}' -H 'Content-Type: application/json'
```
{
    "id":"abc",
    "jsonrpc":"2.0",
    "result":{
        "is_valid":true,
        "is_subaddress":true,
        "is_mainnet":false
    }
}
```

### list_address

List all sub addresses.

Inputs: None

Outputs:

* *Address* - Array of sub addresses.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:13805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"list_address","params":{}}' -H 'Content-Type: application/json'

```
{
	"id": "0",
	"jsonrpc": "2.0",
	"result": {
		"address": ["dSxu8ePRuLa7r4NxCX21enBHEMvdJSJUL8ur7kzjCUuZMoPngyRXSfJRRFuLatTUB7XvbhZUChsUsegcwjEVUVNa24UCM2pkjF", 
                    "dSxuPFw4LRRJZyB9BKxPgFHcRNjGsscphTBrq67GL7BwHFU6AveBgQzW9pffG3xsYvbwGCjmbX85xeZU4vRVMGTg1EX7kn8Tt2", 
                    "dSxu8a3NLAH5fNR9XsemYfMpcq1473HBRXUszLWn4SSfgp2MARYTo6DW8r7ywL8H1ZUkEpHjG8h57FnRXu8fZ3sb3eCRuGQMLy", 
                    "dSxu8zUqAo21jq1oWJssE6SssxjvqTB7nKDQ5Dtv1y7q8mLkU3bd8Gc2UhgFwxtXohaBwTJNKyRHRdnoe2RuVsdB21g8DmykVa", 
                    "dSxuLZ3Z75ccR7dhzp52YxBwoUFG8HGAEEyvh1JFM7QMbqLu9eUYvRRCUMRmQp21qbQkXR5HjeeEo5CzttUiDAqPA8rcTSZRvj", 
                    "dSxu5eE1hVXMAq6bQW5FR8JPjnhf48zC76G7tPZBNEPvjGzsY8zMHUz1mKmkKoDKqu5Bt9u45an61RfigF2gnTSw84CJE92eic", 
                    "dSxuA7c4h4iKV2QYARjQ3XMT93TWLcr4mGCCm18Futtoewmq9FcaSgGDUBwybqEaQACZhDdcPTBeLBqSjZ54ZXjbAGr4e2T2Jb", 
                    "dSxuGEYRAWXX62ENNfgeApaPTgWuoZTp6675BWWB8D9W7vvjp9y5AnZaML5hJaPspJUh6YpAmEYtQ6E85sUTrwXq3wv5xD47pE"]
	}
}
```

### create_address

Create new sub addresses.

Inputs:

* *Count* - Number of addresses to be created, the default is `1`.

Outputs:

* *Address* - Array of sub addresses.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:13805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"create_address","params":{}}' -H 'Content-Type: application/json'

```
{
	"id": "0",
	"jsonrpc": "2.0",
	"result": {
		"address": ["dSxu8ePRuLa7r4NxCX21enBHEMvdJSJUL8ur7kzjCUuZMoPngyRXSfJRRFuLatTUB7XvbhZUChsUsegcwjEVUVNa24UCM2pkjF"]
	}
}
```

### buy_share

Buy share for PoS profits.

Inputs:

* *pool_id* - string; The id of the pool which votes this share.
* *reward* - string; Address to receive profits.
* *num* - int; Number of votes to buy.

Outputs:

* *txid* - string; Transaction hash.
* *share_id* - string; Unique id of this share.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:13805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"buy_share","params":{"reward":"dST1N9V3fSCer4xc2WT36qAs7Gh8fJXP4cP6M7Le53gHBZTLTAYRVw4U4rLciqoXL5TD8Y5JTin3BPMAButd9BtM6srtBinHvL","pool_id":"fd8eeaafd81bddad1caac1762f8564bed591380fb8c4df4da80d6c038f6c2ab7", "num": 1}}' -H 'Content-Type: application/json'

```
{
	"id": "0",
	"jsonrpc": "2.0",
	"result": {
    "share_id": "2c1f6b585f55cf690ac18700870e15145f006bd39df4ac84689a67b880fd17ad",
    "txid": "bdff871ecbf7b73e038a2f764b7db3d2caacac16abaedc6a464c0e24c2577aa8"
	}
}
```

### repo_share

Repo a previously bought share, coins would be unlocked after repo.

Inputs:

* *share_id* - string; The share id.

Outputs:

* *txid* - string; Transaction hash.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:13805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"repo_share","params":{"share_id":"2c1f6b585f55cf690ac18700870e15145f006bd39df4ac84689a67b880fd17ad"}}' -H 'Content-Type: application/json'

```
{
	"id": "0",
	"jsonrpc": "2.0",
	"result": {
		"txid": "2f9990e1532efa9363a06274413eaa16aded310e0370357374b946fff7c961bc"
	}
}
```

### split_integrated_address

Retrieve the standard address and payment id corresponding to an integrated address.

Inputs:

* *integrated_address* - string

Outputs:

* *standard_address* - string
* *payment* - string; hex encoded

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"split_integrated_address","params":{"integrated_address": "dTiruDXg1RCPqFVZF8aEy6HGJ5G4B1AuW98SofzynZxeWondBpJtzYMGJPb13mAAsDhocLfZxBjFA5K5CTqVkgDpGc4qgMizgdB64xBovXArW"}}' -H 'Content-Type: application/json'
```
{
	"id": 1,
	"jsonrpc": "2.0",
	"result": {
		"standard_address": "dTw7r5qoAQXNY9vm9NzPxUB2rTr5VZMDhSXuS7mdAd7X9fhEYLgQrBCMCJvz9zuA6WhdTMSocjCScaHWcNqH2XKi1eSpz1ARH",
		"payment_id": "e585429c6685bd52"
	}
}
```

### get_transfer_by_txid

Retrieve the transfer details by hash.

Inputs:

* *txid* - string

Outputs:

* *payments* - see [get_transfers](#get_transfers)

### NOTE
* a transfer could be of type `in` or `out`, make sure the `type` are correctly checked.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_transfer_by_txid","params":{"txid": "2a6547c79a40884b67d2852cc01aeab75d4b8883311a9f687365ca3edc97516d"}}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"payments": {
			"tx_hash": "2a6547c79a40884b67d2852cc01aeab75d4b8883311a9f687365ca3edc97516d",
			"block_height": 1001,
			"topo_height": 1101,
			"amount": 58935115068,
			"unlock_time": 0,
			"destinations": null,
			"type": "in"
		}
	}
}
```

### get_transfers

Retrieve the transfer details.

Inputs:

* *in* - boolean; (defaults to false) Include incoming transfers.
* *out* - boolean; (defaults to false) Include outgoing transfers.
* *pending* - boolean; (defaults to false) Include pending transfers.
* *failed* - boolean; (defaults to false) Include failed transfers.
* *pool* - boolean; (defaults to false) Include transfers from the wallet's transaction pool.
* *filter_by_height* - boolean; (Optional) Filter transfers by height.
* *min_height* - int; (Optional) Minimum height to scan for transfers, if filtering by height is enabled (defaults to 0).
* *max_height* - int; (Optional) Maximum height to scan for transfers, if filtering by height is enabled (defaults to wallet height).

### NOTE
* since `pending` defaults to `false`, thus transactions of the latest unstable blocks are not returned, use `true` if the unstable are needed.
* both `min_height` and `max_height` are height, **NOT** topo height.

Outputs:

* *in* - in array of transfers:
  * *tx_hash* - string; Transaction ID for this transfer.
  * *payment_id* - string; Payment ID for this transfer.
  * *amount* - unsigned int; Amount transferred.
  * *address* - string; The subaddress receiving the funds, empty if receiver is not subaddress.
  * *timestamp* - unsigned int; Time when block was mined.
  * *fee* - unsigned int; Transaction fee for this transfer.
  * *block_height* - unsigned int; Height of the block containing this transfer.
  * *topo_height* - unsigned int; Topo height of the transfer.
  * *unlock_time* - unsigned int; Number of blocks until transfer is safely spendable.
  * *type* - string; `in` for incoming, `out` for outgoing.
  * *destinations* - array of:
    * *amount* - unsigned int; Amount transferred.
    * *address* - string; Public address of the transfer.
* *out* - out array of transfers(see above)

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_transfers","params":{"in":true, "out": true, "filter_by_height":true,"min_height":1000,"max_height":1001}}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"in": [{
			"tx_hash": "2a6547c79a40884b67d2852cc01aeab75d4b8883311a9f687365ca3edc97516d",
			"block_height": 1001,
			"timestamp": 1587366171,
			"amount": 58935115068,
			"unlock_time": 0,
			"destinations": null,
			"type": "in"
		}, {
			"tx_hash": "70e3719c201f64bd48b1445cf85d4022db8f9d04d4c0b9aa694f863dfe10b9b2",
			"block_height": 1000,
			"timestamp": 1587366171,
			"amount": 58935115259,
			"unlock_time": 0,
			"destinations": null,
			"type": "in"
		}]
	}
}
```

