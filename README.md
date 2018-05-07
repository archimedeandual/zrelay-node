# Z-transaction relay node

A bridge between the transaparent and anonymous blockhain Z-snarks world. Useful for sending anonymous transactions from light wallets.

Wait for transaprent transactions. Explore the embedded data content (OP\_RETURN). 
If the content is encrypted with its self public key and data follows the Z-relay standard, extracts the Z destination address and send an anonymous transaction.

Data format is JSON as follows:

```
 { "zrelay": {"version": 1, "id":"<string>", "destzaddr":"<z_addr>", "amount":<int>, "acktaddr":"<t_addr>"} }
```

+ **dstzaddr** is the destination Z address where **amount** will be sent
+ **fee** is calculated from amounts difference. Fee must be two or three times the single fee:
  + if acktaddr present, three transactions are required so totalfee=fee\*3
  + if acktaddr not present, totalfee=fee\*2 (one for shielding and other for the actual z-tx)
+ **acktaddr** is an optional T address for sending the result of the operation
+ **id** is a string which identifies the operation. It's also used for randomizing the encrypted content.
+ **version** is always 1 until new version appears

## Usage

After cloning repository, update submodules:

```
git submodule init
git submodule update
```

Export an address and its keys to a file (will be used for Z-relay operations):

`./zrelay --port 39967 --auth vocdoni:vocdoni -k  > mykey`

Example for sending a Z-relay transaction using [blockmessage](https://github.com/vocdoni/blockmessage/) (from any other node of the blockchain).

```
./blockmessage.py --port 39967 --auth vocdoni:vocdoni -i mykey -s RBx4HTje7LAQNkzAdQdnxztX9oQkacpeyU:5.00000003  \
	--text '{ "zrelay":{"version":1, "id":"firstZTXever", "acktaddr":"RBx4HTje7LAQNkzAdQdnxztX9oQkacpeyU","destzaddr":"zcMgL78dad7iExP5YeYk4oeNhtzJ1Kvh9SqfzVC6vStEJhgPEadg6pTU1EWnhnR9NwF9EQ7RrbQnLuoWKSNcCfZu2kFufyA","amount":5} }' \
	--encrypt --pubkey 03360daec2591105e8c53f145c9f7682826ddaeb4a20e4dd34e0b760d7c71903d1
```

Do the relay for new messages.

```
./zrelay --port 39967 --auth vocdoni:vocdoni -i mykey

My address: RBx4HTje7LAQNkzAdQdnxztX9oQkacpeyU
My pubkey:03360daec2591105e8c53f145c9f7682826ddaeb4a20e4dd34e0b760d7c71903d1

-> Processing new message
 message id	theID8475
 destination	zcMgL78dad7iExP5YeYk4oeNhtzJ1Kvh9SqfzVC6vStEJhgPEadg6pTU1EWnhnR9NwF9EQ7RrbQnLuoWKSNcCfZu2kFufyA
 acknowledge	RBx4HTje7LAQNkzAdQdnxztX9oQkacpeyU
 send amount	5.00000000
 txn fee	0.00000001
-> Shielding TX funds
 shield addr	zcUcPAUxszReuPrFnoC9gk63mNwNxYqnmJmGQS7H6xtYMaBr99hLHyuTQgLTCfxuYd7Kji28sBatZwtuJcSkiJADCeRXeSc
-> Sending 5.000000 to zcMgL78dad7iExP5YeYk4oeNhtzJ1Kvh9SqfzVC6vStEJhgPEadg6pTU1EWnhnR9NwF9EQ7RrbQnLuoWKSNcCfZu2kFufyA
-> Successful opid-93b61536-edf3-4af1-8108-21fca370264e
No more messages
```

## Help

```
usage: zrelay [-h] [--host RPC_HOST] [--port RPC_PORT] [--auth RPC_AUTH]

optional arguments:
  -h, --help       show this help message and exit
  --host RPC_HOST  RPC host to connect
  --port RPC_PORT  RPC port to connect
  --auth RPC_AUTH  RPC authentication params (username:password)
  -k               Print wallet keys and exit
  -i KEYSFILE      File containing own keys (generated with -k)
```

