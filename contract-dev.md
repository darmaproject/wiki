# Darma（TestNet）Smart Contract Development Tutorial

DarmaCash smart contract supports C language. Smart contracts contain state variables and methods which can modify/access state variables.

# Create a smart contract

It is recommended to refer to the existing [contract-examples](https://github.com/darmaproject/contract-examples) example for beginners, which contains the relevant C language header file darmalib.h

# Compile smart contract

Download [dwarf](https://github.com/darmaproject/dwarf/releases) compiler, use the following command to compile and generate abi file and compress file when smart contract is ready.

./dwarf compile -code usdt.c

# Deploy smart contract

Refer to the DarmaCash smart contract [JS tutorial](https://github.com/darmaproject/darma.js/blob/master/doc/contract-reference.md).

# Constructor
A smart contract needs to have one and only one constructor. The constructor allows passing parameters, but there is no return value. The constructor will only be executed once during the entire contract execution cycle. The constructor is declared by the keyword `constructor`, followed by the name of the constructor, which is also the name of the smart contract.

```c
#include "darmalib.h"

constructor ContractSample(){

}
```


# State variables

State variables include primitive data types such as `int32`, `int64`, `uint32`, `uint64`, `uint256`, `string`, `address`, `bool` and composite data types such as `mapping`, `array` and `struct`. State variables are declared through the KEY keyword. Immutable constants can be modified with const. For primitive data types and `struct` types composed of primitive data types, initial assignment can be performed at the same time when they are declared. State variables must be declared in accordance with global variables in C language. The assignment and reading of state variables are consistent with the C language.

## Declaration and initialization of state variables

```c
KEY int32 var1;
KEY int32 var2 = 1;
KEY const int32 var3;
KEY string var4;
KEY string var5 = "string";
KEY uint256 var6 = U256(1000000000000);
KEY address var7;
KEY address var8 = AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD");
KEY int32 var9,var10;
KYE int32 var11 = 1,var12 = 2;
```

Note:

* Assignment to the `uint256` type requires the keyword `U256` to convert the number.
* Assignment to the `address` type must use the `AddressFrom` function to convert the string.
* Usage of `U256` and `U256From`: `U256` can be used for `uint256` type initialization and conversion of numeric constant in the method, `U256From` can only be used for conversion of string constants and variables in the method.

```c
KEY uint256 var1 = U256(1000000000000);
KEY address var82 = AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD");

void functionbody(){
  string a ="0x1";
  string b = "10000";
  address addr1 = AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD");
  address addr2 = addr1;

  uint256 addr1 = U256(1000);
  uint256 addr2 = U256From("100000");
  uint256 addr2 = U256From(b);
}

```

## Declaration and initialization of struct type state variables

`struct` type is same as C language

```c
struct S1{
     int32 a;
};
KEY struct S1 var1;
KEY struct S1 var2 = {1};
//
KEY struct {
    int32 a;
} var3;
KEY struct {
    int32 a;
} var4 = {1};

//
typedef struct S2{
     int32 a;
} s2;
KEY s2 var5;
KEY s2 var5 = {1};

```

```c
KEY struct {
  string str;
  address addr;
  uint256 u256;
  uint64 u64;
} s1 = {"teststringinstruct", AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD"), U256(10000000000011),
        1000001};

KEY struct {
  string str;
  address addr;
  uint256 u256;
  uint64 u64;
  struct {
    string str;
    address addr;
    uint256 u256;
    uint64 u64;
  } s;
} s2 = {"teststringinstruct",
        AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD"),
        U256(10000000000011),
        1000001,
        {"teststringinstructstruct", AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD"),
         U256(1000000000001122), 10000012}};
```

## Declaration and initialization of `array` type state variables

The `array` type variable is used to store any type of data with a numeric index of uint64. The `array` has three elements: `index`, `value` and `length`. The `length` is set for the array by length, associates any type of value and `index` through the joint assignment of `index` and `value`. The parameter when array is declared is the type of value.

```c
KEY array(string) var1;

var1.length = 1;
var1.index = 0;
var1.value = "value";

var1.index = 0;
string val = var1.value;

```

Note:

* The `length` of the `array` must be set before the assignment of the array.
* Assignment and read operations need to be assigned to `index` in advance, and then `value` assignment and read operations can be associated with the previously defined `index`.
* For multiple assignments to `index`, `value` will only be associated with the closest `index` in the code.
* The `value` of `array` can be any type, so it is possible to nest `array` in `array`.
* `Array` does not support initialization in the declaration.

## Declaration and initialization of mapping type state variables

The `mapping` type variable is used to store any type of data with a simple type index. `Mapping` has two elements: `key` and `value`. Any type of value and key are associated through the joint assignment of key and value. 
When mapping is declared, the first parameter is the type of key, and the second parameter is the type of value.

```c
//mapping declaration
KEY mapping(int32,string) var1;

//mapping write
var1.key = 0;
var1.value = "value";

//mapping read
var1.key = 0;
string val = var1.value;

```

Note:

* Assignment and read operations need to be assigned to the `key` in advance, and then the `value` assignment and read operation can be associated with the previously defined `key`.
* For multiple assignments to `key`, `value` will only be associated with the closest `key` in the code.
* The `value` of `mapping` can be of any type, even `mapping` itself.
* `Mapping` does not support initialization in the declaration.

## Compound declaration of mapping, array and struct

The `value` of `mapping` and `array` can use any type. The variables in `struct` can use any type. Therefore, `mapping`, `array` and `struct` can be nested with each other.

### Declaration of nested ``array`` 

```c
KEY array(array(string)) var1;

var1.length = 0;
var1.index = 0 ;
var1.value.length = 1;
var1.value.index = 0;
var1.value.value = "value";

var1.index = 0;
var1.value.index = 0;
string var = var1.value.value;
```

### Declaration of ``array`` nested ``mapping``

```c
//
KEY array(mapping(string,string)) var1;

var1.length = 1;

var1.index = 0;
var1.value.key = "key";
var1.value.value = "value";

var1.index = 0;
var1.value.key = "key";
string var = var1.value.value;

```

### Declaration of ``array`` nested ``struct``

```c
//
KEY array(struct{
     int32 a;
     string b;
}) var1;

var1.length = 1;

var1.index = 0;
var1.value.a = 1;
var1.value.b = "b";

var1.index = 0;
int32 a = var1.value.a;
string b = var1.value.b;

```

### Declaration of ``mapping`` nested ``mapping``

```c

KEY mapping(string,mapping(string,string)) var1;

var1.key = "key1";
var1.value.key = "key2";
var1.value.value = "value";

var1.key = "key1";
var1.value.key = "key2";
string var = var1.value.value;

```

### Declaration of ``mapping`` nested ``array``

```c

KEY mapping(string,array(string)) var1;

//
var1.key = "key";
var1.value.length = 1;
var1.value.index = 0;
var1.value.value = "value";

//
var1.key = "key";
var1.value.index = 0;
string var = var1.value.value;

```

### Declaration of ``mapping`` nested ``struct``

```c

KEY mapping(string,struct{
   int32 a;
   string b;
}) var1;

//write
var1.key = "key";
var1.value.a = 0;
var1.value.b = "b";

//read
var1.key = "key";
int32 a = var1.value.a;
string b = var1.value.b;

```

### Declaration of ``struct`` nested ``array``

```c
KEY struct{
     int32 a;
     array(string) b;
} var1;

//write
var1.a = 1;

var1.b.length = 1;
var1.b.index = 0;
var1.b.value = "value";

//read
int32 a1 = var1.a;

var1.b.index = 0;
string b1 = var1.b.value;

```

### Declaration of ``struct`` nested ``mapping``

```c
KEY struct{
     int32 a;
     mapping(string,string) b;
} var1;

//write
var1.a = 1;

var1.b.key = "key";
var1.b.value = "value";

//read
int32 a1 = var1.a;

var1.b.key = "key";
string b1 = var1.b.value;

```

### Declaration of ``struct`` nested ``struct``

```c
KEY struct{
     int32 a;
     struct{
          int32 b;
          string c;
     } d;
} var1;

//write
var1.a = 1;

var1.d.b = 1;
var1.d.c = "value";

//read
int32 a = var1.a;
int32 b = var1.d.b;
string c = var1.d.c;

```

### Multilevel nesting

For `mapping`, `array`, `struct` multi-level nesting, assignment and read operation shall follow:
* Before assigning or reading value, you need to assign the `key` or `index` in all levels.
* For the `array` in the nest, before assignment to the `array`, you need to set the `length` of the `array`, otherwise the `length` of the `array` is 0

## Functions

Smart contracts access and modify state variables through `function`. Like C language, `function` can have multiple parameters and only one return value.
`Function` can be modified with `MUTABLE` or `UNMUTABLE`, `MUTABLE` or `UNMUTABLE` need to be written in the previous line of function. Modified functions are externally accessible, unmodified functions are internal functions and cannot be accessed externally.

## Externally accessible functions

The parameter types and return value types of functions that can be accessed externally are restricted and currently only **simple types** are allowed.

* Input parameter types: `int32`, `int64`, `uint32`, `uint64`, `uint256`, `string`, `address`, `bool`
* Return value types: `int32`, `int64`, `uint32`, `uint64`, `uint256`, `string`, `address`, `bool` and `void`
* Input parameters and return values do not support pointers, `mapping`, `array`, `struct` and other custom types

There are 4 types of functions that can be accessed externally, `MUTABLE` and `UNMUTBALE`, `Payabl`e and `Unpayable`.

### ``MUTABLE``

The method modifier `MUTABLE` will change the state, so access to this method needs to initiate a transaction.

### ``UNMUTBALE``

`UNMUTABLE` does not modify state variables, so no transaction is required to access this method.

The following situation is considered as the state variable is modified:

* State variable write operation
* Call `event`
* Send native tokens
* Call `MUTABLE` functions across contracts

***

### ``Payable``

The `Payable` function can receive native tokens from the transaction, using the symbol `$` to indicate that `$` needs to be written at the front of the method name

Declaration of `Payable` function:

```c
MUTABLE
uint32 $testfunction1(int32 var1,string var2,address var3){

}
```

### ``Unpayable``

If there is no `$` symbol at the beginning of the method name, it means that the method is `Unpayable`, and it is forbidden to receive native tokens from the transaction

Declaration of `Unpayable` function:

```c
MUTABLE
uint32 testfunction1(int32 var1,string var2,address var3){

}

UNMUTABLE
uint32 testfunction2(int32 var1,string var2,address var3){

}
```

## Internal function

Internal functions follow the definition of C language functions without any restrictions.

# EVENT

`EVENT` provides an abstraction for the log function of `WAVM`. The application can subscribe and listen to these events through the client's `RPC` interface.
The event is declared by the keyword `EVENT`. The event only needs to declare the method name and parameters, and the event has no return value.
Event parameter types: `int32`, `int64`, `uint32`, `uint64`, `uint256`, `string`, `address`, `bool`
The parameters of the event can be modified by `indexed`, the modified parameters will be indexed, `indexed` need to be written in front of the parameter type
The calling of events is the same as the calling of methods in C language


```c
//declare event
EVENT testEvent(indexed int32 var1,string var2);

//call event
testEvent(1,"string"）;

```

# Cross smart contract call

`CALL` provides a way for cross smart contract access, the object of access is a function in the smart contract that can be accessed externally
The `CALL` method is declared by the keyword CALL, and the definition rule of the CALL method is:

* The first parameter type must be the structure `CallParams`, the structure contains `address`, `amoun`t and `gas` parameters that need to be initialized.
* The second (if there are parameters) and the following parameters are the parameters of the function in the called smart contract that can be accessed externally.
* The return value is the return value of the function in the called smart contract that can be accessed externally.

```c
//contract a for being called
MUTABLE
uint32 test(int32 var1,string var2){
    ...
}
```

```c
//contract b, call contract a
CALL uint32 test(CallParams params,int32 var1,string var2);

MUTABLE
uint32 testcall(){
     CallParams prams = {AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD"), U256(10000), 100000};
     uint32 res = test(prams, 1, "string");
     ...
}
```

# Fallback

`Fallback` is a optional function. If the contract implements the `Fallback` function, when the input data for executing the contract is incorrect or empty, wavm will enter the `Fallback` function for execution. The `Fallback` function has no parameters and no return value.

`Fallback` function declaration:

```c
_(){ //unpayable fallback
     ...
}

$_(){ //payable fallback
  ...
}

```

Note:

* When both payable fallback and unpayable fallback appear in a contract, only unpayable fallback will be executed.

# Appendix: Types and Standard Library

1, Basic types
==========
Boolean type
----
``bool``:``true`` and ``false``.
Supported operators:
*  ``!`` (Logic NOT)
*  ``&&`` (Logic AND)
*  ``||`` (Logic OR)
*  ``==`` (Equal)
*  ``!=`` (Unequal)

Integer type
-------
``int32`` / ``uint32`` / ``int64`` / ``uint64``


Supported operators:

* Comparison: ``<=``, ``<``, ``==``, ``!=``, ``>=``, ``>``
* Bitwise operations: ``&``, ``|``, ``^`` (and，or，exclusive or)
* Arithmetic operators: ``+``, ``-``, ``*``, ``/``, ``%``, ``<<``, ``>>``

Large number type
-------
``uint256``: ``uint256`` literal requires the keyword U256 to represent

``int256``: unimplemented

```c
uint256 u = U256(1000000000000000000);
```

Supported methods:

* ``uint256 U256_Add(uint256 x, uint256 y)`` addition
* ``uint256 U256_Sub(uint256 x, uint256 y)`` Subtraction
* ``uint256 U256_Mul(uint256 x, uint256 y)`` multiplication
* ``uint256 U256_Div(uint256 x, uint256 y)`` division
* ``uint256 U256_Pow(uint256 x, uint256 y)`` Power operation
* ``int32 U256_Cmp(uint256 x, uint256 y)`` comparison operation

String type
-------------------
``string``: string type must be represented by `""` double quotes

```c
string str = "string";
```

Address type
--------
`address`: The `address` type is a 32-byte special character string, which needs to be expressed by the keyword Address. When the ordinary address string is converted to an address, the AddressFrom function must be used.

```c
address addr = AddressFrom("dTw8yKUhy4FbtvLrhaXdUpKbSNqNkoAh9LZ5FWf8V5NDGjKmoLc4GJPShCeErjBsBe6mNGAN2QvD8ieSzVmAofaK1DSq3PnYD");
```


2, Type conversion
==========

| from\to | int32    | int64    | uint32    | uint64    | string     | address        | uint256        | bool    |
| ------- | -------- | -------- | --------- | --------- | ---------- | -------------- | -------------- | ------- |
| int32   |          | (int64)x | (uint32)x | (uint64)x | FromI64(x) |                | U256FromI64(x) | (bool)x |
| int64   | (int32)x |          | (uint32)x | (uint64)x | FromI64(x) |                | U256FromI64(x) | (bool)x |
| uint32  | (int32)x | (int64)x |           | (uint64)x | FromU64(x) |                | U256FromU64(x) | (bool)x |
| uint64  | (int32)x | (int64)x | (uint32)x |           | FromU64(x) |                | U256FromU64(x) | (bool)x |
| string  | ToI64(x) | ToI64(x) | ToU64(x)  | Tou64(x)  |            | AddressFrom(x) | U256From(x)    |
| address |          |          |           |           |            |                |                |
| uint256 |          |          |           |           | x          |                |                |
| bool    | (int32)x | (int64)x | (uint32)x | (uint64)x | FromI64(x) |                | U256FromI64(x) |


3, Standard library method

| name                  | description                                                                                          | type                                  | return type       | gas consumption                                                                                                                              |
| --------------------- | ------------------------------------------------------------------------------------------------- | ----------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| GetSender             | Get transaction initiator address                                                                               |                                           | address          | 2 + 40                                                                                                                               |
| GetOrigin             | Get contract creator address                                                                          |                                           | address          | 2 + 40                                                                                                                               |
| GetValue              | Get native amount when creating or calling contract                                                            |                                           | uint256          | 2 + 64                                                                                                                               |
| GetBalanceFromAddress | Get native balance of address                                                                        | address addr                              | uint256          | 2 + 64                                                                                                                               |
| GetContractAddress    | Get the current contract address                                                                        |                                           | address          | 2 + 40                                                                                                                               |
| GetBlockHash          | Get block hash by its topo height                                  | uint64 blocknumber                        | string           | 20 + 64                                                                                                                              |
| GetBlockNumber        | Get current block height                                                                                      |                                           | uint64           | 2                                                                                                                                    |
| GetTimestamp          | Get current block timestamp                                                                              |                                           | uint64           | 2                                                                                                                                    |
| GetBlockProduser      | Get current block producer's address(unsupported)                                                                              |                                           | address          | 2 + 40                                                                                                                               |
| SHA3                  | SHA3 crypto                                                                                      | string data                               | string           | 30 + 6 * size of string data                                                                                                         |
| GetGas                | Get remaining gas                                                                                       |                                           | uint64           | 2                                                                                                                                    |
| GetGasLimit           | Get gas limit of current transaction                                                                            |                                           | uint64           | 2                                                                                                                                    |
| Assert                | if it fails, it returns the msg, the transaction failes and all gas is consumed. This is used for debugging, use require in production environment | bool condition, string msg                | void             | true : 2 ; false : all gas                                                                                                           |
| Revert                | Roll back the transaction and interrupt the contract                                | string msg                                | void             | 2  + 2 * size of string data                                                                                                         |
| Require               | Determine whether the condition is true, if it fails, the transactions failes                   | bool condition, string msg                | void             | true : 0 ; false : 2  + 2 * size of string data                                                                                      |
| SendFromContract      | Transfer amount to addr, the transfer will be reverted if fails                              | (address addr, uint256 amount)            | void             | 2300                                                                                                                                 |
| TransferFromContract  | Transfer amount to addr, returns false if fails                                                | (address addr, uint256 amount)            | bool             | 2300                                                                                                                                 |
| FromI64               | Convert the value of int64 to string                                             | int64 value                               | string           | 2                                                                                                                                    |
| FromU64               | Convert the value of uint64 to string                                                      | uint64 value                              | string           | 2                                                                                                                                    |
| ToI64                 | Convert string into int64                                                                           | string value                              | int64            | 2                                                                                                                                    |
| ToU64                 | Convert string into uint64                                                                              | string value                              | uint64           | 2                                                                                                                                    |
| Concat                | Concatenate two strings                                                                       | string str1, string str2                  | string           | 2 * (size of str1 and str2)                                                                                                          |
| Equal                 | Determine whether two strings are equal                                                      | char *str1, char *str2                    | bool             | 2                                                                                                                                    |
| AddressFrom           | Convert an address string into address type                                                     | string addrStr                            | address          | 2 + 40                                                                                                                               |
| Pow                   | uint64 Power operation                                                                                 | uint64 x, uint64 y                        | uint64           | 50 * int((y bitlen + 7) / 8) + 2                                                                                                     |
| U256From              | string to uint256                                                                                 | string x                                  | uint256          | 2 + 64                                                                                                                               |
| U256FromU64           | uint64 to uint256                                                                                 | uint64 x                                  | uint256          | 3 + 64                                                                                                                               |
| U256FromI64           | int64 to uint256                                                                                  | int64 x                                   | uint256          | 3 + 64                                                                                                                               |
| U256_Add              | uint256 addtion                                                                                      | uint256 x, uint256 y                      | uint256          | 3 + 64                                                                                                                               |
| U256_Sub              | uint256 subtraction                                                                                | uint256 x, uint256 y                      | uint256          | 3 + 64                                                                                                                               |
| U256_Mul              | uint256 multiplication                                                                          | uint256 x, uint256 y                      | uint256          | 3 + 64                                                                                                                               |
| U256_Div              | uint256 division                                                                                 | uint256 x, uint256 y                      | uint256          | 3 + 64                                                                                                                               |
| U256_Pow              | uint256 power operation                                                                          | uint256 x, uint256 y                      | uint256          | 50 * int((y bitlen + 7) / 8) + 2  + 64                                                                                               |
| U256_Shl              | uint256 left shift operation                                                                       | uint256 value, uint256 shift              | uint256          | 3 + 64                                                                                                                               |
| U256_Shr              | uint256 right shift operation                                                                     | uint256 value, uint256 shift              | uint256          | 3 + 64                                                                                                                               |
| U256_And              | uint256 AND operation                                                                             | uint256 x, uint256 y                      | uint256          | 3 + 64                                                                                                                               |
| U256_Or               | uint256 OR operation                                                                             | uint256 x, uint256 y                      | uint256          | 3 + 64                                                                                                                               |
| U256_Xor              | uint256 XOR operation                                                                           | uint256 x, uint256 y                      | uint256          | 3 + 64                                                                                                                               |
| U256_Cmp              | uint256 comparision operation                                                                   | uint256 x, uint256 y                      | int32            | 3                                                                                                                                    |
| PrintAddress          | Print an address variable                                                                        | string remark, address a                  | void             |                                                                                                                                      |
| PrintStr              | Print an string variable                                                                          | string remark, string s                   | void             |                                                                                                                                      |
| PrintUint64T          | Print an uint64 variable                                                                          | string remark, uint64 num                 | void             |                                                                                                                                      |
| PrintUint32T          | Print an uint32 variable                                                                         | string remark, uint32 num                 | void             |                                                                                                                                      |
| PrintInt64T           | Print an int64 variable                                                                          | string remark, int64 num                  | void             |                                                                                                                                      |
| PrintInt32T           | Print an int32 variable                                                                         | string remark, int32 num                  | void             |                                                                                                                                      |
| PrintUint256T         | Print an uint256 variable                                                                             | string remark, uint256 num                | void             |                                                                                                                                      |
| EVENT                 | Event                                                                                             | variable parameter                                  | void             | 375 + topics * 375 + data * 8                                                                                                        |
| CALL                  | Cross smart contract call                                                                 | variable parameter                                  | any returned type | 700 + (new addr：25000, old addr：0 ) + （value!=0 : 9000 , value=0 : 0） + gas consumption of callee- （value!=0 : 2300 , value=0 : 0） |


4, Gas consumption of data storage and reading on the chain
==========================

Storage
----

There are three cases of gas calculation：
* From zero value to non-zero value -> new data -> gas consumption 20000
* From non-zero value to zero value -> delete data -> gas returns 15000, consume 5000
* From non-zero value to non-zero value -> change data -> gas consumption 5000

Read
----
gas consumption 200


5, Suggested processing methods for complex state variables
======================

For some complex state variables, such as the nesting of `mapping (string, mapping (string, string))`, when reading values or assigning values, usually use the following way

```c
KEY mapping(string,mapping(string,string)) map;

//write
map.key = "testkey1";
map.value.key = "testkey2";
map.value.value = "testvalue";

//read
map.key = "testkey1";
map.value.key = "testkey2";
string val = map.value.value;

```

or

```c
typedef mapping(string,string) mapdef;
KEY mapping(string,mapdef) map;
//write
map.key = "testkey1";
KEY mapdef *tmpmap = &map.value; //temp variable
tmpmap->key = "testkey2";
tmpmap->value = "testvalue";

//read
map.key = "testkey1";
KEY mapdef *tmpmap = &map.value; //temp variable
tmpmap->key = "testkey2";
string val = tmpmap->value;
```

For the case of nested structures within `mapping`

```c
typedef struct
{
  int32 a;
  int64 b;
  uint32 c;
  uint64 d;
  string e;
  address f;
  bool g;
  uint256 h;
} _s;

KEY mapping(string, _s) map_with_struct;
//write
map_with_struct.key = key;
map_with_struct.value.a = a;
map_with_struct.value.b = b;
map_with_struct.value.c = c;
map_with_struct.value.d = d;
map_with_struct.value.e = e;
map_with_struct.value.f = f;
map_with_struct.value.g = g;
map_with_struct.value.h = h;

//read
map_with_struct.key = key;
int32 a = map_with_struct.value.a;
...
uint256 h = map_with_struct.value.h;

```

or

```c
typedef struct
{
  int32 a;
  int64 b;
  uint32 c;
  uint64 d;
  string e;
  address f;
  bool g;
  uint256 h;
} _s;

//========write========
//for named structures
KEY mapping(string, _s) map_with_struct;
map_with_struct.key = key;
KEY _s s; //temp variable
s.a = a;
s.b = b;
s.c = c;
s.d = d;
s.e = e;
s.f = f;
s.g = g;
s.h = h;
map_with_struct.value = s;

// for anonymous structures and named structure
map_with_struct.key = key;
KEY _s *s; //temp variable
s = &map_with_struct.value;
s->a = a;
s->b = b;
s->c = c;
s->d = d;
s->e = e;
s->f = f;
s->g = g;
s->h = h;
//========Assignment ========

//========read========
//for named structures
map_with_struct.key = key;
KEY _s S0 =  map_with_struct.value //temp variable
int32 a = s0.a;
...
uint256 h = s0.h;

// for anonymous structures and named structure
map_with_struct.key = key;
KEY _s *s0 = &map_with_struct.value; //temp variable
int32 a = s0->a;
...
uint256 h = s0->h;
//========read========

```

Note:
* The above several different ways of value assignment are also applicable to `array` and more complex combination types.
* The defined temporary variables need to use the keyword `KEY`.
* `Mapping` and `array` are essentially anonymous structures, which can only be read or assigned using the usual methods and anonymous structures


6, Random number
========

Generating random numbers is very difficult in a verifiable deterministic system.
In the case where the randomness of random numbers is not strict, random data on the blockchain such as tx hash and nonce can be used.

7, SendFromContract,TransferFromContract,ContractCall
========================================

All three methods can send a certain amount of DMCH to an address.

SendFromContract
----------------

* When the execution fails, execute revert.
* Fixed consumption of 2300 gas to prevent re-entry attacks.
* It should be used in most cases because this is the safest way to send DMCH.

TransferFromContract
--------------------

* When execution fails, return false.
* Fixed consumption of 2300 gas to prevent re-entry attacks.
* Used in the very few cases where you want to deal with sending failures in the contract.

If the DMCH addresses sent by `SendFromContract` and `TransferFromContract` are contract addresses, the contract must implement the `payable` `fallback` method, otherwise it will fail to execute. At the same time, due to the fixed consumption of 2300 gas, only a small amount of operations can be performed in the fallback method so that the gas does not exceed

ContractCall
------------

* When the execution fails, all gas is consumed
* Cannot safely prevent re-entry attacks
* Used when you need to control gas and need to access methods in other contracts
