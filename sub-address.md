# **Darma(TestNet) Sub Address Tutorial**

Sub addresses can be used both in cli and rpc interfaces, using rpc interfaces requires wallet's `--rpc-server`, see below:

* darma-wallet-cli --testNet --rpc-server --rpc-bind=127.0.0.1:13805 --wallet-file contract.wallet --password WALLETPASSWORD
* Parameter Description:
   * --rpc-server：enable rpc interface(If not specified, it will not be enabled by default.)
   * --rpc-bind：specify the IP address and port bound to the rpc interface
   * --wallet-file：specify the wallet file.(If the wallet is not specified, please follow the command line to create it.)
   * --password：specify the password of the wallet (this parameter is not recommended for security reason. The program will prompt you to enter the password when wallet password is not specified.)
   * For more parameters, use `--help`

## **Usage**

* [CLI](#CLI)
* [RPC](#RPC)
  * [Create Sub Address](#create-address)
  * [List Sub Address](#list-address)

## **CLI**

Note: You need to enter the prompt mode(type 'e' in menu mode) before using sub addresses in the cli <br/>

Type 'create_address' to create a new sub address <br/>

Type 'list_address' to show all sub addresses created before

## **RPC**

### **create-address**

Create sub address(es)

* method: create_address
* inputs:
  * count - int; create `count` addresses at a time
* outputs:
  * address - array; addresses created

Example:
```
curl -X POST http://127.0.0.1:13805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"create_address","params":{"count":3}}' -H 'Content-Type: application/json'

// output
{
	"id": "0",
	"jsonrpc": "2.0",
	"result": {
		"address": ["dST1CdusEh74HiAV2SPWPZ4U1GfwBgwcxPnFVzQWco2DJ2SYEbvkRyKctCPEuXVUMcHBVueBgTp3f2Ur2BAA9wK41yNxHsC78x", "dST1Tby6ydx6Qsnpw2KSb3CAXPRpi2rKvN4uzDVSzftD8o1iweaYF6KKfFh2BJF3HyJ5DJu8pBEfZAdUqMgJHHvr5tjGLGNoer", "dST1V3phjmybSyLX2WYCWS35wFUC4tWdPSLU6Lr3W9nhbQhEAXtvmDrD1EgQj5wrg1Q7NbyzE1JeT1C7Hmx3nTPC8pENmjpHLq"]
	}
}
```

### **list-address**

List all sub addresses of the wallet

* method: list_address
* inputs:
* outputs:
  * address - array; all addresses created before

Example:
```
curl -X POST http://127.0.0.1:13805/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"list_address","params":{}}' -H 'Content-Type: application/json'

// output
{
	"id": "abc",
	"jsonrpc": "2.0",
	"result": {
		"address": ["dST1EVbpfyQY114kmvnbGx6V9Ap6F5CPiSPGkPr9sgbaXjmWH53pJYngcAwYJztYQZe83x6n7Z6hfeQU6cbcLBXQ3S3h1r7KY8", "dST1CdusEh74HiAV2SPWPZ4U1GfwBgwcxPnFVzQWco2DJ2SYEbvkRyKctCPEuXVUMcHBVueBgTp3f2Ur2BAA9wK41yNxHsC78x", "dST1Tby6ydx6Qsnpw2KSb3CAXPRpi2rKvN4uzDVSzftD8o1iweaYF6KKfFh2BJF3HyJ5DJu8pBEfZAdUqMgJHHvr5tjGLGNoer", "dST1V3phjmybSyLX2WYCWS35wFUC4tWdPSLU6Lr3W9nhbQhEAXtvmDrD1EgQj5wrg1Q7NbyzE1JeT1C7Hmx3nTPC8pENmjpHLq", "dST1Y76yksiZ1CtibTwYdWfp3WjCcjNzY4FUHR6Nted4f7mLa61RLoC9k2cm7dg3aCU2xmY1RqQhz831XXdi3CbE3mcKuYZo6v", "dST1Pdd2bAkcGhJSyHnJQKh62kBQSVRDRYKS4GJ1rPhXEYeXqGsLHQqTyMKDnFKJMr1txrhMnDvVWQK8LAsvKEzQ14dtvpii7D"]
	}
}
```