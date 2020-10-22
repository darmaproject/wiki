## Introduction

This is a simplified version of rpc documentation which is mostly helpful for exchanges.

All methods use the same JSON RPC interface. For example:

```
curl -X POST http://127.0.0.1:33805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"make_integrated_address","params":{}}' -H 'Content-Type: application/json'
```

Note: "atomic units" refer to the smallest fraction of 1 DMCH according to the darmad implementation. **1 DMCH = 1e8 atomic units.**

### Index of Wallet(darma-wallet-cli) RPC Methods:

* [getbalance](#getbalance)
* [getaddress](#getaddress)
* [getheight](#getheight)
* [transfer](#transfer)
* [make_integrated_address](#makeintegratedaddress)
* [split_integrated_address](#splitintegratedaddress)
* [get_transfer_by_txid](#get_transfer_by_txid)
* [get_transfers](#get_transfers)

### Index of Daemon(darmad) RPC Methods:

* [/sendrawtransaction](#sendrawtransaction)

### Useful Tips:

* [tip1](#tip1)
Suggestions on how exchanges scan transaction records：

* [tip2](#tip2)
Initiate multiple withdrawals at one time

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
* If `do_not_relay` is true, the transaction won't be relayed to the chain and can be relayed later by [/sendrawtransaction](#sendrawtransaction).
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

### /sendrawtransaction

Broadcast a raw transaction to the network.

Inputs:

* *tx_as_hex* - string; Hex encoded raw transaction datam, see [transfer](#transfer)

Outputs:

* *status* - string; General RPC error code. "OK" means everything looks good.

Example (No return information included here.):

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/sendrawtransaction -d '{"tx_as_hex":"de6a3..."}' -H 'Content-Type: application/json'

## Useful Tips:

### tip1
### Suggestions on how exchanges scan transaction records：

- Continuously scan transaction records according to the latest block height, call the get_transfers method to scan the transaction records (set the pending parameter to true), if there is a new transaction "A", then it is the received transaction "A" in  pending status.
- When the block height changes, get the "stableheight" from get_info, if the block height of transaction A is less than "stableheight", then the transaction "A" is mature.
- Call the get_transfer_by_txid method to confirm whether the transaction "A" is on the chain. If it is on the chain, then the transaction "A" is confirmed/valid.

Example:

get_transfers returned value:

{"id":"1"
"jsonrpc":"2.0"
"result":{"in":[{"index_global":992678
"tx_hash":"467cbd2e9c87d525d735ac9dc3f4c66da6c69a51dc02e9feaf5b34feada1ad20"
"block_hash":"7c0ec164495f71aea3d82948ff22470a98598ec02e6ed5dff4108838e6ff2b1f"
"block_height":229331
"topo_height":314426
……
}

get_info returned value:

{"id":"1111111"
"jsonrpc":"2.0"
"result":{"alt_blocks_count":0
"difficulty":2766709637
"grey_peerlist_size":0
"height":238617
"stableheight":238609
"topoheight":325946
……
}

"block_height":229331 < "stableheight":238609 = tx is confirmed/valid.

### tip2
### Initiate multiple withdrawals at one time

The transfer method can construct multiple vouts in the same transaction to realize simultaneous transfer to multiple different destination addresses (a single transaction can have up to 7 destination addresses). Please refer to "https://github.com/darmaproject/wiki/blob/master/Wallet-RPC-Documentation.md#transfer"
