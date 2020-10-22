## Introduction

This is a list of the darmad daemon RPC calls, their inputs and outputs, and examples of each.

Many RPC calls use the daemon's JSON RPC interface while others use their own interfaces, as demonstrated below.

Note: "atomic units" refer to the smallest fraction of 1 DMCH according to the darmad implementation. **1 DMCH = 1e8 atomic units.**

### [JSON RPC Methods](#json-rpc-methods):

* [getblockcount](#getblockcount)
* [on_getblockhash](#ongetblockhash)
* [getblocktemplate](#getblocktemplate)
* [submitblock](#submitblock)
* [getlastblockheader](#getlastblockheader)
* [getblockheaderbyhash](#getblockheaderbyhash)
* [getblockheaderbyheight](#getblockheaderbyheight)
* [getblockheaderbytopoheight](#getblockheaderbytopoheight)
* [getoutputs](#getoutputs)
* [check_tx_key](#check_tx_key)
* [getblock](#getblock)
* [get_info](#get_info)
* [gettxpool](#gettxpool)

### [Other RPC Methods](#other-daemon-rpc-calls):

* [/getheight](#getheight)
* [/getoutputs.bin](#getoutputs.bin)
* [/gettransactions](#gettransactions)
* [/sendrawtransaction](#sendrawtransaction)
* [/is_key_image_spent](#is_key_image_spent)


---

## JSON RPC Methods

The majority of darmad RPC calls use the daemon's `json_rpc` interface to request various bits of information. These methods all follow a similar structure, for example:

```
    IP=127.0.0.1
    PORT=33804
    METHOD='getblockheaderbyhash'
	PARAMS='{"hash":"3986ad2457457e9a4d03d9def0cf2f1cfa4cd2bddccb25f057a61fee8d01b946"}'
	curl \
		-X POST http://$IP:$PORT/json_rpc \
		-d '{"jsonrpc":"2.0","id":"0","method":"'$METHOD'","params":'$PARAMS'}' \
		-H 'Content-Type: application/json'
```

Some methods include parameters, while others do not. Examples of each JSON RPC method follow.

### getblockcount

Look up how many blocks are in the longest chain known to the node.

Inputs: *None*.

Outputs:

* *count* - unsigned int; Number of blocks in longest chain seen by the node.
* *status* - string; General RPC error code. "OK" means everything looks good.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getblockcount"}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": {
    "count": 993163,
    "status": "OK"
  }
}
```

### on_getblockhash

Look up a block's hash by its topo height.

Inputs:

* block topo height (int array of length 1)
  
Outputs:

* block hash (string)

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"on_getblockhash","params":[912345]}' -H 'Content-Type: application/json'
```
{
  "id": "0",
  "jsonrpc": "2.0",
  "result": "e22cf75f39ae720e8b71b3d120a5ac03f0db50bba6379e2850975b4859190bc6"
}
```

### getblocktemplate

Inputs:

* *wallet_address* - string; Address of wallet to receive coinbase transactions if block is successfully mined.
* *reserve_size* - unsigned int; Reserve size.

Outputs:

* *blocktemplate_blob* - string; Blob on which to try to mine a new block.
* *blockhashing_blob* - string; Hash blob on which to try to mine a new block.
* *expected_reward* - unsigned int; Expected block reward.
* *difficulty* - unsigned int; Difficulty of next block.
* *height* - unsigned int; Height on which to mine.
* *prev_hash* - string; Hash of the most recent block on which to mine the next block.
* *reserved_offset* - unsigned int; Reserved offset.
* *epoch* - unsigned int; Epoch timestamp.
* *status* - string; General RPC error code. "OK" means everything looks good.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getblocktemplate","params":{"wallet_address":"dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD","reserve_size":60}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"blocktemplate_blob": "0505efa29af505a3e752d300000000000000000000000000000000000000000000000000000000000000008b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0000002e71201ffab12010002df46400a592be10e1c8c9663a598309c9d2c3f781bcab574650ab030b1580f132101abfa1663595db1818b16ffc06284938bc7303afbd8c2f1f5f23a038795f319d6000000000000000000000000000000000000000000000000000000000000000000014e9155c40b14f648aa4cb3ec5cb30c1ba8c928de12dc42b3fcd92cfcccc993ad00",
		"blockhashing_blob": "05056f91a65e0019f559bbd311e96152c36ed4fcfb1d8a4d2e4d297fdfdf12941a83f467fd4735a3e752d3000000000000000000000000000000000000000000000000000000000000000000",
		"expected_reward": 0,
		"difficulty": 80,
		"height": 2347,
		"prev_hash": "4e9155c40b14f648aa4cb3ec5cb30c1ba8c928de12dc42b3fcd92cfcccc993ad",
		"reserved_offset": 43,
		"epoch": 1587974526,
		"status": "OK"
	}
}
```

### submitblock

Submit a mined block to the network.

Inputs:

* blob data and hash data - (string array of length 2)

Outputs:

* *status* - string; Block submit status.
* *blid* - string; Block hash.


### getlastblockheader

Block header information for the most recent block is easily retrieved with this method. No inputs are needed.

Inputs: *None*.

Outputs:

* *block_header* - A structure containing block header information.
  * *depth* -  unsigned int; The number of blocks succeeding this block on the blockchain. A larger number means an older block.
  * *difficulty* - unsigned int; The strength of the darma network based on mining power.
  * *hash* - string; The hash of this block.
  * *height* - unsigned int; Chain height.
  * *topoheight* - unsigned int; The number of blocks preceding this block on the blockchain.
  * *major_version* - unsigned int; The major version of the darma protocol at this block height.
  * *minor_version* - unsigned int; The minor version of the darma protocol at this block height.
  * *nonce* - unsigned int; a cryptographic random one-time number used in mining a darma block.
  * *orphan_status* - boolean; Usually `false`. If `true`, this block is not part of the chain.
  * *syncblock* - boolean; Whether this block is a sync block.
  * *main_block* - boolean; Whether this block is a main block. A main block is block on main chain.
  * *txcount* - unsigned int; The number of transactions in this block.
  * *reward* - unsigned int; The amount of new atomic units generated in this block and rewarded to the miner.
  * *pos_reward* - unsigned int; The amount of new atomic units scheduled in this block for the stake holders.
  * *generated_pos_reward* - unsigned int; The amount of new atomic units generated in this block for the stake holders.
  * *tips* - string array; Parents' hash of this block.
  * *votes* - string array; PoS shares voted on this block.
  * *state_root* - string; Smart contract state root hash.
  * *timestamp* - unsigned int; The time the block was recorded into the blockchain.
* *status* - string; General RPC error code. "OK" means everything looks good.

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getlastblockheader"}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"block_header": {
			"depth": 0,
			"difficulty": "80",
			"hash": "244054e83a12c7b5fb444b50e14262a05e19335b4127b93697e293f143420463",
			"height": 2777,
			"topoheight": 2777,
			"major_version": 5,
			"minor_version": 5,
			"nonce": 100663296,
			"orphan_status": false,
			"syncblock": false,
			"main_block": true,
			"txcount": 0,
			"reward": 58934776323,
			"tips": ["1314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e5"],
			"state_root": "8b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0",
			"timestamp": 1587975446
		},
		"status": "OK"
	}
}
```

### getblockheaderbyhash

Block header information can be retrieved using either a block's hash or height. This method includes a block's hash as an input parameter to retrieve basic information about the block.

Inputs:

* *hash* - string; The block hash.

Outputs:

* *block_header* - A structure containing block header information. See [getlastblockheader](#getlastblockheader).

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getblockheaderbyhash","params":{"hash":"244054e83a12c7b5fb444b50e14262a05e19335b4127b93697e293f143420463"}}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"block_header": {
			"depth": 0,
			"difficulty": "80",
			"hash": "244054e83a12c7b5fb444b50e14262a05e19335b4127b93697e293f143420463",
			"height": 2777,
			"topoheight": 2777,
			"major_version": 5,
			"minor_version": 5,
			"nonce": 100663296,
			"orphan_status": false,
			"syncblock": false,
			"main_block": true,
			"txcount": 0,
			"reward": 58934776323,
			"tips": ["1314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e5"],
			"state_root": "8b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0",
			"timestamp": 1587975446
		},
		"status": "OK"
	}
}
```

### getblockheaderbyheight

Similar to `getblockheaderbyhash` above, except that this method returned an array of multiple block headers.

Inputs:

* *height* - unsigned int; The block's height.

Outputs:

* *block_headers* - Array of structure containing block header information. See [getlastblockheader](#getlastblockheader).

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getblockheaderbyheight","params":{"height":2777}}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"block_headers": [{
			"depth": 273,
			"difficulty": "80",
			"hash": "244054e83a12c7b5fb444b50e14262a05e19335b4127b93697e293f143420463",
			"height": 2777,
			"topoheight": 2777,
			"major_version": 5,
			"minor_version": 5,
			"nonce": 100663296,
			"orphan_status": false,
			"syncblock": true,
			"main_block": true,
			"txcount": 0,
			"reward": 58934776323,
			"tips": ["1314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e5"],
			"state_root": "8b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0",
			"timestamp": 1587975446
		}],
		"status": "OK"
	}
}
```

### check_tx_key

Verify whether a transaction was actually sent to the address provided.

Inputs:

* *tx_key* - string; The transaction's private key.
* *address* - string; The destination address.
* *txid* - string; The transaction hash.

Outputs:

* *tx_info*
	* *address* - string; The address provided.
	* *txid* - string; The transaction hash provided.
	* *amount* - unsigned int; The output amount in atomic units.
	* *paymentid* - string; The payment id.

darma@darma:~$</span> curl -X POST http://127.0.0.1:13804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"check_tx_key","params":{"tx_key":"e7a5745eac4f80f89e1345635cc1d95f80e794f7073711fa4bc2838812c0b401", "address":"dST1ZJWccAwAdrAmJf3YM6V8K8hdwu9UiA3kMvgEexxAZtNiWYwqeXh4Jh9njo6W5WRVmtCysRwmF3CL3dVAScf9AAc1tJTehT","txid":"3dc17c6bbb2839fdbc9434db6cc78a60b8dcadb88c9cb3da4dc9686ad315708f"}}' -H 'Content-Type: application/json'
```
{
    "id":"abc",
    "jsonrpc":"2.0",
    "result":{
        "status":"OK",
        "tx_info":{
            "address":"dST1ZJWccAwAdrAmJf3YM6V8K8hdwu9UiA3kMvgEexxAZtNiWYwqeXh4Jh9njo6W5WRVmtCysRwmF3CL3dVAScf9AAc1tJTehT",
            "index_within_tx":1,
            "amount":12000000,
            "paymentid":""
        }
    }
}
```

### getoutputs

Get outputs of a block by hash or topoheight.

Inputs:

* *height* - unsigned int; The block's **topo height**.
* *hash* - string; The block's hash.

Outputs:

* *outputs* - Array of structure containing block outputs.
	* *blid* - string; The block hash.
	* *txid* - string; The transaction hash.
	* *amount* - unsigned int; The output amount in atomic units(for normal transactions, the value is 0).
	* *height* - unsigned int; The block height.
	* *Topoheight* - unsigned int; The block topo height.
	* *txtype* - int; Type of the transaction, 5 for pool proft, 6 for share profit, 7 for miner profit, 8 for PoS season bonus.

darma@darma:~$</span> curl -X POST http://127.0.0.1:53804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getoutputs","params":{"height":12345}}' -H 'Content-Type: application/json'
```
{
    "id":"0",
    "jsonrpc":"2.0",
    "result":{
        "outputs":[
            {
                "blid":"cf906b545e32406e53a3b682e237b81a40f2123e7b0c5f0dc36473a29e5470ae",
                "txid":"9484b810fe4c835d38aa32799c6763d4d8c85bb79a6f81fda8518b13aada0fb5",
                "publickey":"e4ccfdccbe6d84d860bae70c2d28738e17215b673de7e1196d1ca30a8b38f05d",
                "inkey":{
                    "Destination":"1301e99dbaeacc66468c274ed359245ab54989eb7705adaa2b40420b508a6205",
                    "Mask":"a76f8b876e22e501bce39ecd2a4a8bf52892c9ae42a5d6a2ef02ba74debd1118"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":35589420,
                "indexglobal":12701,
                "height":11736,
                "Topoheight":12345,
                "Block_Time":1592526178,
                "txfee":0,
                "txtype":7,
                "target":{
                    "Key":"EwHpnbrqzGZGjCdO01kkWrVJiet3Ba2qK0BCC1CKYgU="
                },
                "rewardpoolid":"0000000000000000000000000000000000000000000000000000000000000000",
                "rewardshareid":"0000000000000000000000000000000000000000000000000000000000000000",
                "Key_Images":null
            }]
    }
}
```

#### For blocks containing 3 PoS votes, each vote has 2 outputs(one for pool and the other for share), thus at least 7 outputs are returned.
#### Only main block has PoS outputs.

darma@darma:~$</span> curl -X POST http://127.0.0.1:53804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getoutputs","params":{"height":225400}}' -H 'Content-Type: application/json'
```
{
    "id":"abc",
    "jsonrpc":"2.0",
    "result":{
        "outputs":[
            {
                "blid":"b4c4bfc2909b85b974181c7a741d606859c3540d639c3e65d08a77b7ea8d93a3",
                "txid":"830301e9be3bdc5bb7eead0a04b071288fe77c89980ac138b5de2b81468e2254",
                "publickey":"428fac8fdb52cc9e89a4cb3c52903180d967a92960cd09fd6daa2dd1072cb711",
                "inkey":{
                    "Destination":"18666665af1e9a48ea733975fe99ad85999095686fff76a92d9d8d3bd7a9b5e5",
                    "Mask":"c9c09fc847fba44f2d474f16a85815771dfb9a3d2d2c6f2d269a07525bec6a50"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":33789530,
                "indexglobal":542180,
                "height":169454,
                "Topoheight":225400,
                "Block_Time":1594888715,
                "txfee":0,
                "txtype":7,
                "target":{
                    "Key":"GGZmZa8emkjqczl1/pmthZmQlWhv/3apLZ2NO9epteU="
                },
                "rewardpoolid":"0000000000000000000000000000000000000000000000000000000000000000",
                "rewardshareid":"0000000000000000000000000000000000000000000000000000000000000000",
                "Key_Images":null
            },
            {
                "blid":"b4c4bfc2909b85b974181c7a741d606859c3540d639c3e65d08a77b7ea8d93a3",
                "txid":"b7bb38375ab84bce4f36a24028afdae682d8de4d8acee1a30b4e93228f9e0491",
                "publickey":"3c4db91692d2af60073651cc697a25ea747be8ce47ff2d56ba5fb896b6d0b475",
                "inkey":{
                    "Destination":"1b46b9aad499be159f955efe475a61c2ba6eb5208ae499b8ec392812410b38df",
                    "Mask":"7e64c136ee5df97ed9b1b1408ecc1fe7473d027a182453659692151a6e60250b"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":22526353,
                "indexglobal":542181,
                "height":169454,
                "Topoheight":225400,
                "Block_Time":1594888715,
                "txfee":0,
                "txtype":5,
                "target":{
                    "Key":"G0a5qtSZvhWflV7+R1phwrputSCK5Jm47DkoEkELON8="
                },
                "rewardpoolid":"c8e3883932e0b4c413aebe131c4d2b74d0e2012b5cf0f79b7965e6d8949c4905",
                "rewardshareid":"0000000000000000000000000000000000000000000000000000000000000000",
                "Key_Images":null
            },
            {
                "blid":"b4c4bfc2909b85b974181c7a741d606859c3540d639c3e65d08a77b7ea8d93a3",
                "txid":"b7bb38375ab84bce4f36a24028afdae682d8de4d8acee1a30b4e93228f9e0491",
                "publickey":"afd800115609531a10e2469f1334771c4ce04a1c2fe97e69d08065bdb210cabf",
                "inkey":{
                    "Destination":"a0bff384d343293481c6de8da995d098d518fab15bdefe286c19de803fcbbf8e",
                    "Mask":"77140ca122c975e2449323128712a20bae4d4d6ee49a3bc75731dc83fc9eab57"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":123894943,
                "indexwithintx":1,
                "indexglobal":542182,
                "height":169454,
                "Topoheight":225400,
                "Block_Time":1594888715,
                "txfee":0,
                "txtype":6,
                "target":{
                    "Key":"oL/zhNNDKTSBxt6NqZXQmNUY+rFb3v4obBnegD/Lv44=",
                    "PubKey":"r9gAEVYJUxoQ4kafEzR3HEzgShwv6X5p0IBlvbIQyr8="
                },
                "rewardpoolid":"c8e3883932e0b4c413aebe131c4d2b74d0e2012b5cf0f79b7965e6d8949c4905",
                "rewardshareid":"686464f91cbcdcf948d6804f0dbfb2310d0cc15b8bc9535bba5dc8a7fd159e7a",
                "Key_Images":null
            },
            {
                "blid":"b4c4bfc2909b85b974181c7a741d606859c3540d639c3e65d08a77b7ea8d93a3",
                "txid":"b7bb38375ab84bce4f36a24028afdae682d8de4d8acee1a30b4e93228f9e0491",
                "publickey":"3c4db91692d2af60073651cc697a25ea747be8ce47ff2d56ba5fb896b6d0b475",
                "inkey":{
                    "Destination":"763a15bddd87be66758695b15d2072f6659ff35ab107763a955045bb1cd5de01",
                    "Mask":"7e64c136ee5df97ed9b1b1408ecc1fe7473d027a182453659692151a6e60250b"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":22526353,
                "indexwithintx":2,
                "indexglobal":542183,
                "height":169454,
                "Topoheight":225400,
                "Block_Time":1594888715,
                "txfee":0,
                "txtype":5,
                "target":{
                    "Key":"djoVvd2HvmZ1hpWxXSBy9mWf81qxB3Y6lVBFuxzV3gE="
                },
                "rewardpoolid":"6e32aab2b182f7c645dbdfa824a0a4600c768127c32c1457bde607cfb73fd925",
                "rewardshareid":"0000000000000000000000000000000000000000000000000000000000000000",
                "Key_Images":null
            },
            {
                "blid":"b4c4bfc2909b85b974181c7a741d606859c3540d639c3e65d08a77b7ea8d93a3",
                "txid":"b7bb38375ab84bce4f36a24028afdae682d8de4d8acee1a30b4e93228f9e0491",
                "publickey":"f534bda673f1929fa350bd75647785747a98a2fe3a2f86fe2dc8739645d58e33",
                "inkey":{
                    "Destination":"dd3241acf0091a352f92e0f1c4ca23890b17da2f62156ae4a6a1e513fcd91f89",
                    "Mask":"77140ca122c975e2449323128712a20bae4d4d6ee49a3bc75731dc83fc9eab57"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":123894943,
                "indexwithintx":3,
                "indexglobal":542184,
                "height":169454,
                "Topoheight":225400,
                "Block_Time":1594888715,
                "txfee":0,
                "txtype":6,
                "target":{
                    "Key":"3TJBrPAJGjUvkuDxxMojiQsX2i9iFWrkpqHlE/zZH4k=",
                    "PubKey":"9TS9pnPxkp+jUL11ZHeFdHqYov46L4b+LchzlkXVjjM="
                },
                "rewardpoolid":"6e32aab2b182f7c645dbdfa824a0a4600c768127c32c1457bde607cfb73fd925",
                "rewardshareid":"9493172155eed058ee7310e4854f1c1ce275981b2b0d13f955f6ddf00537cef9",
                "Key_Images":null
            },
            {
                "blid":"b4c4bfc2909b85b974181c7a741d606859c3540d639c3e65d08a77b7ea8d93a3",
                "txid":"b7bb38375ab84bce4f36a24028afdae682d8de4d8acee1a30b4e93228f9e0491",
                "publickey":"3c4db91692d2af60073651cc697a25ea747be8ce47ff2d56ba5fb896b6d0b475",
                "inkey":{
                    "Destination":"3d866de1ed6c06a715806643dc8646b96a945d501ced7c95f465c023e25c6a79",
                    "Mask":"c467b53efad43c068bb7903a505cd63cf763368a557a1eae763c673d15c4ea03"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":22526354,
                "indexwithintx":4,
                "indexglobal":542185,
                "height":169454,
                "Topoheight":225400,
                "Block_Time":1594888715,
                "txfee":0,
                "txtype":5,
                "target":{
                    "Key":"PYZt4e1sBqcVgGZD3IZGuWqUXVAc7XyV9GXAI+Jcank="
                },
                "rewardpoolid":"3c7f08dbe9fdf5b55e6a5f6dedd71a1d1194da7b0d8b726f02c23bae99840147",
                "rewardshareid":"0000000000000000000000000000000000000000000000000000000000000000",
                "Key_Images":null
            },
            {
                "blid":"b4c4bfc2909b85b974181c7a741d606859c3540d639c3e65d08a77b7ea8d93a3",
                "txid":"b7bb38375ab84bce4f36a24028afdae682d8de4d8acee1a30b4e93228f9e0491",
                "publickey":"a1272683717b5552395e07783965aafb73bf8a5fce258a199299158f88f5c932",
                "inkey":{
                    "Destination":"32d0bc9e230ef4f2cfc310be987a4252bc57d3b5edb46ff6e5ab80a1adc34c98",
                    "Mask":"8a440cc879d81f72287fcb90251d2fc002e0394341b5bc609e66895246cc5d26"
                },
                "ecdhtuple":{
                    "Mask":"0000000000000000000000000000000000000000000000000000000000000000",
                    "Amount":"0000000000000000000000000000000000000000000000000000000000000000"
                },
                "senderpk":"0000000000000000000000000000000000000000000000000000000000000000",
                "amount":123894944,
                "indexwithintx":5,
                "indexglobal":542186,
                "height":169454,
                "Topoheight":225400,
                "Block_Time":1594888715,
                "txfee":0,
                "txtype":6,
                "target":{
                    "Key":"MtC8niMO9PLPwxC+mHpCUrxX07XttG/25auAoa3DTJg=",
                    "PubKey":"oScmg3F7VVI5Xgd4OWWq+3O/il/OJYoZkpkVj4j1yTI="
                },
                "rewardpoolid":"3c7f08dbe9fdf5b55e6a5f6dedd71a1d1194da7b0d8b726f02c23bae99840147",
                "rewardshareid":"f2401c35c226853214cf58821569b09a9a0bd430a24c20b7548fb0f803972bab",
                "Key_Images":null
            }]
    }
}
```

### getblock

Full block information can be retrieved by either block topo height or hash, like with the above block header calls. For full block information, both lookups use the same method, but with different input parameters.

Inputs (pick one of the following):

* *height* - unsigned int; The block's topo height.
* *hash* - string; The block's hash.

Outputs:

* *blob* - string; Hexadecimal blob of block information.
* *block_header* - A structure containing block header information. See [getlastblockheader](#getlastblockheader).
* *json* - json string; JSON formatted block details:
  * *major_version* - Same as in block header.
  * *minor_version* - Same as in block header.
  * *timestamp* - Same as in block header.
  * *nonce* - Same as in block header.
  * *miner_tx* - Miner transaction information
    * *version* - Transaction version number.
    * *unlock_time* - The block height when the coinbase transaction becomes spendable.
    * *vin* - List of transaction inputs:
      * *height* - This block height, a.k.a. when the coinbase is generated.
    * *vout* - List of transaction outputs. Each output contains:
      * *amount* - The amount of the output, in atomic units. Usually `0`.
      * *target* - 
        * *key* - The stealth destination address.
    * *extra* - Extra data of transaction.
    * *RctSignature* - RingCT signatures data.
  * *tx_hashes* - List of hashes of non-coinbase transactions in the block. If there are no other transactions, this will be an empty list.
* *status* - string; General RPC error code. "OK" means everything looks good.

**Look up by height:**

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getblock","params":{"height":2777}}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"blob": "050596aa9af5050000000600000000000000000000000000000000000000000000000000000000000000008b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0000002951601ffd9150100026646d651a21d7431594dcc3b58191b7354ec9c75438ac0695317bc468be9038b2101961dd712205081114b91c5d53f077b94aa3bd5ebe03318b6838bf88560c346d9000000000000000000000000000000000000000000000000000000000000000000011314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e500",
		"json": "{\"major_version\":5,\"minor_version\":5,\"timestamp\":1587975446,\"nonce\":100663296,\"state_root\":\"8b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0\",\"vote\":null,\"stake_tx\":{\"version\":0,\"unlock_time\":0,\"Vin\":null,\"Vout\":null,\"Extra\":null,\"RctSignature\":null},\"miner_tx\":{\"version\":2,\"unlock_time\":2837,\"Vin\":[{\"Height\":2777}],\"Vout\":[{\"Amount\":0,\"Target\":{\"Key\":\"6646d651a21d7431594dcc3b58191b7354ec9c75438ac0695317bc468be9038b\"}}],\"Extra\":\"AZYd1xIgUIERS5HF1T8He5SqO9Xr4DMYtoOL+IVgw0bZ\",\"RctSignature\":{\"Message\":\"0000000000000000000000000000000000000000000000000000000000000000\",\"MixRing\":null,\"ECdhInfo\":null,\"OutPk\":null,\"Txid\":\"0000000000000000000000000000000000000000000000000000000000000000\",\"BulletSigs\":null,\"MlsagSigs\":null}},\"tips\":[\"1314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e5\"],\"tx_hashes\":null}",
		"block_header": {
			"depth": 307,
			"difficulty": "80",
			"hash": "244054e83a12c7b5fb444b50e14262a05e19335b4127b93697e293f143420463",
			"height": 2777,
			"topoheight": 2777,
			"major_version": 5,
			"minor_version": 5,
			"nonce": 100663296,
			"orphan_status": false,
			"syncblock": true,
			"main_block": true,
			"txcount": 0,
			"reward": 58934776323,
			"tips": ["1314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e5"],
			"state_root": "8b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0",
			"timestamp": 1587975446
		},
		"status": "OK"
	}
}
```

**Look up by hash:**

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"getblock","params":{"hash":"244054e83a12c7b5fb444b50e14262a05e19335b4127b93697e293f143420463"}}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"blob": "050596aa9af5050000000600000000000000000000000000000000000000000000000000000000000000008b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0000002951601ffd9150100026646d651a21d7431594dcc3b58191b7354ec9c75438ac0695317bc468be9038b2101961dd712205081114b91c5d53f077b94aa3bd5ebe03318b6838bf88560c346d9000000000000000000000000000000000000000000000000000000000000000000011314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e500",
		"json": "{\"major_version\":5,\"minor_version\":5,\"timestamp\":1587975446,\"nonce\":100663296,\"state_root\":\"8b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0\",\"vote\":null,\"stake_tx\":{\"version\":0,\"unlock_time\":0,\"Vin\":null,\"Vout\":null,\"Extra\":null,\"RctSignature\":null},\"miner_tx\":{\"version\":2,\"unlock_time\":2837,\"Vin\":[{\"Height\":2777}],\"Vout\":[{\"Amount\":0,\"Target\":{\"Key\":\"6646d651a21d7431594dcc3b58191b7354ec9c75438ac0695317bc468be9038b\"}}],\"Extra\":\"AZYd1xIgUIERS5HF1T8He5SqO9Xr4DMYtoOL+IVgw0bZ\",\"RctSignature\":{\"Message\":\"0000000000000000000000000000000000000000000000000000000000000000\",\"MixRing\":null,\"ECdhInfo\":null,\"OutPk\":null,\"Txid\":\"0000000000000000000000000000000000000000000000000000000000000000\",\"BulletSigs\":null,\"MlsagSigs\":null}},\"tips\":[\"1314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e5\"],\"tx_hashes\":null}",
		"block_header": {
			"depth": 307,
			"difficulty": "80",
			"hash": "244054e83a12c7b5fb444b50e14262a05e19335b4127b93697e293f143420463",
			"height": 2777,
			"topoheight": 2777,
			"major_version": 5,
			"minor_version": 5,
			"nonce": 100663296,
			"orphan_status": false,
			"syncblock": true,
			"main_block": true,
			"txcount": 0,
			"reward": 58934776323,
			"tips": ["1314343753066ed33bbe3c3881c662430804890f61859ffce9973285be1822e5"],
			"state_root": "8b8a9fd70ceea8cc9767a91020def987d52c476af95e79a9a54a59b0e939fab0",
			"timestamp": 1587975446
		},
		"status": "OK"
	}
}
```

### get_info

Retrieve general information about the state of your node and the network.

Inputs: *None*.

Outputs:

* *alt_blocks_count* - unsigned int; Number of alternative blocks to main chain.
* *difficulty* - unsigned int; Network difficulty (analogous to the strength of the network)
* *grey_peerlist_size* - unsigned int; Grey Peerlist Size
* *height* - unsigned int; Current length of longest chain known to daemon.
* *stableheight* - unsigned int; Current stable length of longest chain known to daemon.
* *topoheight* - unsigned int; Current block number known to daemon.
* *averageblocktime50* - float; Average block mining time.
* *incoming_connections_count* - unsigned int; Number of peers connected to and pulling from your node.
* *outgoing_connections_count* - unsigned int; Number of peers that you are connected to and getting information from.
* *status* - string; General RPC error code. "OK" means everything looks good.
* *target* - unsigned int; Current target for next proof of work.
* *target_height* - unsigned int; The height of the next block in the chain.
* *testNet* - boolean; States if the node is on the testnet (true) or mainnet (false).
* *top_block_hash* - string; Hash of the highest block in the chain.
* *tx_count* - unsigned int; Total number of non-coinbase transaction in the chain.
* *tx_pool_siz* - unsigned int; Number of transactions that have been broadcast but not included in a block.
* *dynamic_fee_per_kb* - unsigned int; Fee per kb for transaction.
* *total_supply* - unsigned int; Amount in circulation.
* *version* - string; Daemon version.
* *white_peerlist_size* - unsigned int; White Peerlist Size

Following is an example `get_info` call and its return:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_info"}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"alt_blocks_count": 0,
		"difficulty": 80,
		"grey_peerlist_size": 0,
		"height": 3974,
		"stableheight": 3966,
		"topoheight": 3974,
		"averageblocktime50": 1.76,
		"incoming_connections_count": 0,
		"outgoing_connections_count": 0,
		"target": 15,
		"target_height": 0,
		"testNet": true,
		"top_block_hash": "da7cdfe85931f330dd4d43fca5ba4046c28077b89c327f3c2fb9c3fba3f4f6ad",
		"tx_count": 0,
		"tx_pool_size": 0,
		"dynamic_fee_per_kb": 10000000,
		"total_supply": 17342073,
		"median_block_size": 1572864,
		"white_peerlist_size": 0,
		"version": "1.1.2-1101.alpha.cislobog",
		"status": "OK"
	}
}
```

### gettxpool

Retrieve general information about the tx pool of your node.

Inputs: *None*.

Outputs:

* *status* - string; General RPC error code. "OK" means everything looks good.
* *txs* - array of string; Array of tx hashes in pool.

Following is an example `gettxpool` call and its return:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"gettxpool"}' -H 'Content-Type: application/json'
```
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"txs": ["26f23d403bc2fe49c99b90b89173efa6ffb8689df4e4e90ae6430821a5956f25"],
		"status": "OK"
	}
}
```

---

## Other Daemon RPC Calls

Not all daemon RPC calls use the JSON_RPC interface. This section gives examples of these calls.

The data structure for these calls is different than the JSON RPC calls. Whereas the JSON RPC methods were called using the `/json_rpc` extension and specifying a method, these methods are called at their own extensions. For example:

    IP=127.0.0.1
    PORT=33804
    METHOD='gettransactions'
	PARAMS='{"txs_hashes":["d6e48158472848e6687173a91ae6eebfa3e1d778e65252ee99d7515d63090408"]}'
	curl \
		-X POST http://$IP:$PORT/$METHOD \
		-d $PARAMS \
		-H 'Content-Type: application/json'

Note: It is recommended to use JSON RPC where such alternatives exist, rather than the following methods. For example, the recommended way to get a node's height is via the JSON RPC methods [get_info](#getinfo) or [getlastblockheader](#getlastblockheader), rather than [getheight](#getheight) below.


### /getheight

Get the node's current height.

Inputs: *None*.

Outputs:

* *height* - unsigned int; Current height of chain. 
* *stableheight* - unsigned int; Current stable height of chain. 
* *topoheight* - unsigned int; Current number of blocks of chain. 
* *status* - string; General RPC error code. "OK" means everything looks good.

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/getheight -H 'Content-Type: application/json'
```
{
	"height": 4281,
	"stableheight": 4273,
	"topoheight": 4281,
	"status": "OK"
}
```

### /gettransactions

Look up one or more transactions by hash.

Inputs:

* *txs_hashes* - string list; List of transaction hashes to look up.
* *decode_as_json* - boolean; Optional. If set `true`, the returned transaction information will be decoded rather than binary.

Outputs:

* *status* - General RPC error code. "OK" means everything looks good.
* *txs_as_hex* - string; Full transaction information as a hex string.
* *txs_as_json* - json string; (Optional - returned if set in inputs.) List of transaction info:
  * *version* - Transaction version
  * *unlock_time* - If not 0, this tells when a transaction output is spendable.
  * *vin* - List of inputs into transaction:
    * *key* - The public key of the previous output spent in this transaction.
      * *amount* - The amount of the input, in atomic units.
      * *key_offsets* - A list of integer offets to the input.
      * *k_image* - The key image for the given input
  * *vout* - List of outputs from transaction:
    * *amount* - Amount of transaction output, in atomic units.
    * *target* - Output destination information:
      * *key* - The stealth public key of the receiver. Whoever owns the private key associated with this key controls this transaction output.
  * *extra* - Usually called the "payment ID" but can be used to include any random 32 bytes.
  * *signatures* - List of ignatures used in ring signature to hide the true origin of the transaction.
Example 1: Return transaction information in binary format.

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/gettransactions -d '{"txs_hashes":["d6e48158472848e6687173a91ae6eebfa3e1d778e65252ee99d7515d63090408"]}' -H 'Content-Type: application/json'
{
  "status": "OK",
  "txs_as_hex": ["..."]
}

Example 2: Decode returned transaction information in JSON format. Note: the "vout" list has been truncated in the displayed return for space considerations.

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/gettransactions -d '{"txs_hashes":["d6e48158472848e6687173a91ae6eebfa3e1d778e65252ee99d7515d63090408"],"decode_as_json":true}' -H 'Content-Type: application/json'
{
  "status": "OK",
  "txs_as_hex": ["..."],
  "txs_as_json": ["{\n  \"version\": 1, \n  \"unlock_time\": 0, \n  \"vin\": [ {\n      \"key\": {\n        \"amount\": 70000000, \n        \"key_offsets\": [ 35952\n        ], \n        \"k_image\": \"d16908468dff9438a9814fe96bdaa575f06fe8da85772b72e54926428712293d\"\n      }\n    }, {\n      \"key\": {\n        \"amount\": 400000000000000, \n        \"key_offsets\": [ 6830\n        ], \n        \"k_image\": \"c7a7024b763df1181ae6fe821b70669735e38a68162ac02362e33acbe829b605\"\n      }\n    }\n  ], \n  \"vout\": [ {\n      \"amount\": 50000, \n      \"target\": {\n        \"key\": \"f6be43f7be4f06adcb1d06f4a07c637c7359e009cf3e57bb32b8c9ea636509c3\"\n      }\n    }, {\n      \"amount\": 200000, \n      \"target\": {\n        \"key\": \"b0a7a8e32f2b5302552bcd8d85112c62838b1f56cccd844eb9b63e0a732d0353\"\n      }\n    },  ...  \n  ], \n  \"extra\": [ 1, 225, 240, 98, 34, 169, 73, 47, 237, 117, 192, 30, 192, 60, 155, 47, 4, 115, 20, 21, 11, 13, 252, 219, 129, 13, 174, 37, 36, 78, 191, 141, 109\n  ], \n  \"signatures\": [ \"e6a3be8003d481d2855c8127f56871de3d28a4fb52385b999eb986c831c5cc08361c126b0db24a21b6c4299b438ee2be201d44d57a371230b9cd04395ab8c400\", \"8309851abaf2cf2a7091e0cdb9c83704fa7d68838a7a8ef8c178bb55a1e93a038dd18bb4a7549dc056b7a70e037cabd80911a03f427e36f712756d4c00f38f0b\"]\n}"]
}


### /is_key_image_spent

Check if outputs have been spent using the key image associated with the output.

Inputs:

* *key_images* - string list; List of key image hex strings to check.

Outputs:

* *spent_status* - unsigned int list; List of statuses for each image checked. Statuses are follows: 0 = unspent, 1 = spent in blockchain, 2 = spent in transaction pool
* *status* - string; General RPC error code. "OK" means everything looks good.

Example:

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/is_key_image_spent -d '{"key_images":["8d1bd8181bf7d857bdb281e0153d84cd55a3fcaa57c3e570f4a49f935850b5e3","7319134bfc50668251f5b899c66b005805ee255c136f0e1cecbb0f3a912e09d4"]}' -H 'Content-Type: application/json'
{
  "spent_status": [1,2],
  "status": "OK"
}


### /sendrawtransaction

Broadcast a raw transaction to the network.

Inputs:

* *tx_as_hex* - string; Full transaction information as hexidecimal string.

Outputs:

* *status* - string; General RPC error code. "OK" means everything looks good.

Example (No return information included here.):

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/sendrawtransaction -d '{"tx_as_hex":"de6a3..."}' -H 'Content-Type: application/json'


### /get_transaction_pool

Show information about valid transactions seen by the node but not yet mined into a block, as well as spent key image information in the node's memory.

Inputs: *None*.

Outputs:

* *spent_key_images* - List of spent output key images:
  * *id_hash* - string; Key image ID hash.
  * *txs_hashes* - string list; Key image transaction hashes.
* *status* - string; General RPC error code. "OK" means everything looks good.
* *transactions* - List of transactions in the mempool that have not been included in a block:
  * *blob_size* - unsigned int; The size of the full transaction blob.
  * *fee* - unsigned int; The amount of the mining fee included in the transaction, in atomic units.
  * *id_hash* - string; The transaction ID hash.
  * *kept_by_block* - boolean; We do not accept transactions that timed out before, unless set `true`.
  * *last_failed_height* - unsigned int; If the transaction has previously timed out, this tells at what height that occured.
  * *last_failed_id_hash* - string; Like the previous, this tells the previous transaction ID hash.
  * *max_used_block_height* - unsigned int; Tells the height of the most recent block with an output used in this transaction.
  * *max_used_block_hash* - string; Tells the hash of the most recent block with an output used in this transaction.
  * *receive_time* - unsigned int; The Unix time that the transaction was first seen on the network by the node.
  * *tx_json* - json string; JSON structure of all information in the transaction:
    * *version* - Transaction version
    * *unlock_time* - If not 0, this tells when a transaction output is spendable.
    * *vin* - List of inputs into transaction:
      * *key* - The public key of the previous output spent in this transaction.
        * *amount* - The amount of the input, in atomic units.
        * *key_offsets* - A list of integer offets to the input.
        * *k_image* - The key image for the given input
    * *vout* - List of outputs from transaction:
      * *amount* - Amount of transaction output, in atomic units.
      * *target* - Output destination information:
        * *key* - The stealth public key of the receiver. Whoever owns the private key associated with this key controls this transaction output.
    * *extra* - Usually called the "transaction ID" but can be used to include any random 32 bytes.
    * *signatures* - List of ignatures used in ring signature to hide the true origin of the transaction.

Example (Note: Some lists in the returned information have been truncated for display reasons):

darma@darma:~$</span> curl -X POST http://127.0.0.1:33804/get_transaction_pool -H 'Content-Type: application/json'
{
  "spent_key_images": [{
    "id_hash": "1edb9ecc39602040282d326070ad22cb473c952c0d6280c9c4c3b853fb34f3bc",
    "txs_hashes": ["409911b2be02e3f0e930b326c67ab9e74675885ce23d71bb3bd28b62bc3e7803"]
  },{
    "id_hash": "4adb4bb63b3397027340ca4e6c45f4ce2147dfb3a4e0fafdec18834ae594a05e",
    "txs_hashes": ["946f1f4c52e3426a41959c93b60078f314813bc4bdebcf69b8ee11d593b2bd14"]
  },
  ...],
  "status": "OK",
  "transactions": [{
    "blob_size": 25761,
    "fee": 290000000000,
    "id_hash": "11d4cff23e610fac6a2b89187ad61d429a5e226652693dcac5d83d506eb92b96",
    "kept_by_block": false,
    "last_failed_height": 0,
    "last_failed_id_hash": "0000000000000000000000000000000000000000000000000000000000000000",
    "max_used_block_height": 954508,
    "max_used_block_id_hash": "03f96b374778bc059e47b96e2beec2e6d4d9e0ad39afeabdbcd77e1bd5a62f81",
    "receive_time": 1457676127,
    "tx_json": "{\n  \"version\": 1, \n  \"unlock_time\": 0, \n  \"vin\": [ {\n      \"key\": {\n        \"amount\": 70000000000, \n        \"key_offsets\": [ 63408, 18978, 78357, 16560\n        ], \n        \"k_image\": \"7319134bfc50668251f5b899c66b005805ee255c136f0e1cecbb0f3a912e09d4\"\n      }\n    },  ...  ], \n  \"vout\": [ {\n      \"amount\": 80000000000, \n      \"target\": {\n        \"key\": \"094e6a1b187385533665f89db741149f42d95fdc50bdd2a2384bcd1dc5209c55\"\n      }\n    },  ...  ], \n  \"extra\": [ 2, 33, 0, 15, 56, 190, 21, 169, 77, 13, 182, 209, 51, 35, 54, 96, 89, 237, 96, 23, 24, 107, 240, 79, 40, 86, 64, 68, 45, 166, 119, 192, 17, 225, 23, 1, 31, 159, 145, 15, 173, 255, 165, 192, 55, 84, 127, 154, 163, 25, 85, 204, 212, 127, 147, 133, 118, 218, 166, 52, 78, 188, 131, 235, 9, 159, 105, 158\n  ], \n  \"signatures\": [ \"966e5a67fbdbf72d7dc0364b705121a58e0ced7e2ab45747b6b154c05a1afe04fac4aac7f64faa2dc6dd4d51b8277f11e2f2ec7729fac225088befe3b8399c0b71a4cb55b9d0e20f93d305c78cebceff1bcfcfaf225428dfcfaaec630c88720ab65bf5d3399dd1ac82ea0ecf308b3f80d9780af7742fb157692cd60515a7e2086878f082117fa80fff3d257de7d3a2e9cc8b3472ef4a5e545d90e1159523a60f38d16cece783579627124776813334bdb2a2df4171ef1fa12bf415da338ce5085c01e7a715638ef5505aebec06a0625aaa72d13839838f7d4f981673c8f05f08408e8b372f900af7227c49cfb1e1febab6c07dd42b7c26f921cf010832841205\",  ...  ]\n}"
  },
  ...]
}
