# Z-transaction relay node

A bridge between the transaparent and anonymous blockhain Z-snarks world. Useful for sending anonymous transactions from light wallets.

Wait for transaprent transactions. Explore the embedded data content (OP\_RETURN). 
If the content is encrypted with its self public key and data follows the Z-relay standard, extracts the Z destination address and send an anonymous transaction.

Data format is JSON as follows:

```
 { "zrelay": {"version": 1, "id":"<string>", "destzaddr":"<z_addr>", "amount":<float>, "acktaddr":"<t_addr>", "donation":<float>} }
```

+ **dstzaddr** is the destination Z address where **amount** will be sent
+ **fee** is calculated from amounts difference. Fee must be one or two times the single fee:
  + if acktaddr present, two transactions are required so totalfee=fee\*2
  + if acktaddr not present, totalfee=fee\*2 (one for shielding and other for the actual z-tx)
+ **acktaddr** is an optional T address for sending the result of the operation
+ **id** is a string which identifies the operation. It's also used for randomizing the encrypted content.
+ **donation** amount which will be given to the Z-relay owner as monetary donation
+ **version** is always 1 until new version appears

## Usage

Install dependencies. Ubuntu/Debian: `apt install python3-ecdsa python3-pyaes`

After cloning repository, update submodules:

```
git submodule init
git submodule update
```

Export an address and its keys to a file (will be used for Z-relay operations):

`./zrelay --port 39967 --auth vocdoni:vocdoni -k  > mykey`

Example for sending a Z-relay transaction using [blockmessage](https://github.com/vocdoni/blockmessage/) (from any other node of the blockchain).

```
./blockmessage.py --port 39967 --auth vocdoni:vocdoni -i mykey -s RBx4HTje7LAQNkzAdQdnxztX9oQkacpeyU:5.00000001  \
	--text '{ "zrelay":{"version":1, "id":"firstZTXever", "acktaddr":"RBx4HTje7LAQNkzAdQdnxztX9oQkacpeyU","destzaddr":"zcMgL78dad7iExP5YeYk4oeNhtzJ1Kvh9SqfzVC6vStEJhgPEadg6pTU1EWnhnR9NwF9EQ7RrbQnLuoWKSNcCfZu2kFufyA","amount":5} }' \
	--encrypt --pubkey 03360daec2591105e8c53f145c9f7682826ddaeb4a20e4dd34e0b760d7c71903d1 \
	--fee 0.00000001
```

Do the relay for new messages.

```
./zrelay -i ../votchain/mykey --port 39967 --auth vocdoni:vocdoni -l
My address: RQRvWqpQWzJXRLv2HeqskTUDmRyg1iNTKD
My pubkey:038b6cb862ad7d3fce3d3c9575471c09de2012bbf0343a84b13628ae59cc7f0a12

-> Processing new message
 message id     firstZTXever
 destination    zcTLgc7mB9Nbaukxr6Q8wnUuwfu3iEpoeewL1Y9yM6U1rJPZWzmmDhhBxkG5nHvZeJLLVvZL68h2UFDxjc6FxqE3qmjDvNx
 acknowledge    None
 send amount    2.00000000
 txn fee        0.00000001
-> Sending 2.000000 to zcTLgc7mB9Nbaukxr6Q8wnUuwfu3iEpoeewL1Y9yM6U1rJPZWzmmDhhBxkG5nHvZeJLLVvZL68h2UFDxjc6FxqE3qmjDvNx
 waiting for opid-ea34b0ad-572c-4f19-bc2c-e6671de758f1
 ................ successful
 txID 82255ac7a51cbfd6c40b9d9b9e0ad9c03fc0ad45c06ed92bc347bf7769bb9c62

```

## Help

```
usage: zrelay [-h] [--host RPC_HOST] [--port RPC_PORT] [--auth RPC_AUTH] [-l]
              [-k] [-i KEYSFILE]

Z transaction relay node

optional arguments:
  -h, --help       show this help message and exit
  --host RPC_HOST  RPC host to connect
  --port RPC_PORT  RPC port to connect
  --auth RPC_AUTH  RPC authentication params (username:password)
  -l               Enable loop/daemon mode. Read forever for relay messages
  -k               Print wallet keys and exit
  -i KEYSFILE      File containing own keys (generated with -k)
```

