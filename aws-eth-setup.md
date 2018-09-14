[tutorial](https://docs.aws.amazon.com/blockchain-templates/latest/developerguide/blockchain-templates-ethereum.html)

## Setup Amazon EC2 Instance

[Tutorial](https://mlgblockchain.com/setup-ethereum-on-aws-1.html)

**Note** : Make sure to enable port 8545 (for inbound type 'Custom TCP Rule') in Inbouds of *Security group*. We gonna use this port later.

## To connect to EC2
- Download putty gen tool from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Open the tool and load the `.pem` file of your EC2 key-pair
- Store the converted-pem file (which is nothing but a `.ppk` file) in the same dir.
- Open putty tool and first paste the host name of EC2 instance. (you can get the host name once, you press the *connect* button, i.e ec2-13-126-224-63.ap-south-1.compute.amazonaws.com)
- https://www.youtube.com/watch?v=bi7ow5NGC-U 


## Installing geth 
```
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo apt-get update
$ sudo apt-get install ethereum
```

## Setting up geth

### Setup Genesis

- Create a genesis.json file 
- Add following configuration
``` 
{
    "config": {  
        "chainId": 1010, 
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "difficulty": "0x400",
    "gasLimit": "0x8000000",  
    "alloc": {}
}
```
### Initiate geth

`geth init --datadir data`

### Run geth node

`geth --datadir data/ --rpc --rpcaddr ec2-13-126-224-63.ap-south-1.compute.amazonaws.com --rpcport 8545 --rpccorsdomain "*" --networkid 3257 --port 30303 --nodiscover`

Make sure you give public ip for --rpcaddr value 

## Creating account

- Attach a console to the running instance. Change diriectory to the geth 

` geth attach data\geth.ipc`

- Creating an account

`personal.newAccount("password")`
i.e personal.newAccount("account001")

- Viewing accounts

`eth.accounts`

## Mining

```
miner.setEtherbase(eth.accounts[0])
miner.start(1)
```

## Connect Metamask to our one node blockchain

- Open the Metamask, and click on *Custom RPC* and paste the url of our blockchain, i.e. `http://13.126.224.63:8545`.
- 13.126.224.63 is our public ip for our blockchain and 8545 is the rpc port that we opened. 
- Click on save.

## Setting up our 2nd node

- Install geth and setup it with same *genesis.json* file

## Run Node 02 with port 8546

`geth --datadir data/ --rpc --rpcaddr ec2-52-66-45-40.ap-south-1.compute.amazonaws.com --rpcport 8546 --rpccorsdomain "*" --networkid 3257 --port 30304 --nodiscover`

Notice that I have used the same network id as of node 01 but changed the rpcaddr and rpcport configurations.

## Connecting peer

- Get  `enode` value of node 01. It will be like this :

`enode://32674274ca7d4696dd3cc8070749df13ea30613ad76159b2152ac8a0a4254d7bcc11ee667cb4a90951a67bae5c2b7b940a8549028d95c830917ab7908fb2abec@[::]:30303?discport=0`

- Replace public ip of node 01 into the enode value

`enode://32674274ca7d4696dd3cc8070749df13ea30613ad76159b2152ac8a0a4254d7bcc11ee667cb4a90951a67bae5c2b7b940a8549028d95c830917ab7908fb2abec@[ec2-13-126-224-63.ap-south-1.compute.amazonaws.com]:30303?discport=0`

- Attach a new console to the running geth instance in node 02

- Add the node 01 as a peer to node02

`admin.addPeers(enode://32674274ca7d4696dd3cc8070749df13ea30613ad76159b2152ac8a0a4254d7bcc11ee667cb4a90951a67bae5c2b7b940a8549028d95c830917ab7908fb2abec@[ec2-13-126-224-63.ap-south-1.compute.amazonaws.com]:30303?discport=0)`



