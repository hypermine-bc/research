
## Setup Amazon EC2 Instances

[Tutorial](https://mlgblockchain.com/setup-ethereum-on-aws-1.html)

**Note** : Make sure to enable ports 8545 and 30303 (for inbound type 'Custom TCP Rule') in Inbouds of *Security group*. We gonna use this port later.

Similarly create another EC2 instance for node 02 with port 8546 opened.

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
        "chainId": 3257, 
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

`geth init genesis.json --datadir data`

### Run geth node 01 with rpcport 8545

`geth --datadir data/ --rpc --rpcaddr ec2-13-232-209-126.ap-south-1.compute.amazonaws.com --rpcport 8545 --rpccorsdomain "*" --networkid 3257 --port 30303 --mine`

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

### Run geth node 02 with --bootenodes option

`geth --datadir data/ --rpc --rpcaddr ec2-52-66-155-129.ap-south-1.compute.amazonaws.com --rpcport 8545 --rpccorsdomain "*" --networkid 3257 --port 30304 --bootnodes enode://0ddb2e6ec112953581692f89ffd2c108dd1080ea3d8b9b05372d5fbba6a12e189f8f07a6a7db71c1306f5210f585cd3d770d557c9de2f9380f5e943ab0803287@13.232.209.126:30303`

Notice that I have used the same network id as of node 01 but changed the rpcaddr and port configurations.

### Connecting peer

> If you have used `--bootnodes` option with the geth command, then you can skip the this section

- Get  `enode` value of node 01. It will be like this :

`enode://0ddb2e6ec112953581692f89ffd2c108dd1080ea3d8b9b05372d5fbba6a12e189f8f07a6a7db71c1306f5210f585cd3d770d557c9de2f9380f5e943ab0803287@[::]:30303`

- Replace public ip of node 01 into the enode value

`enode://0ddb2e6ec112953581692f89ffd2c108dd1080ea3d8b9b05372d5fbba6a12e189f8f07a6a7db71c1306f5210f585cd3d770d557c9de2f9380f5e943ab0803287@13.232.209.126:30303`

- Attach a new console to the running geth instance in node 02

- Add the node 01 as a peer to node02

`admin.addPeers("enode://0ddb2e6ec112953581692f89ffd2c108dd1080ea3d8b9b05372d5fbba6a12e189f8f07a6a7db71c1306f5210f585cd3d770d557c9de2f9380f5e943ab0803287@13.232.209.126:30303")`

# Notes
- The networkid and chainId should be same for both the nodes.
- Make sure you give public ip for --rpcaddr value. 
- Do not use `--nodiscover` option with Geth command.
- If you get any error, then use `--verbosity 10` option with the geth command to see the log.

# References

[tutorial](https://docs.aws.amazon.com/blockchain-templates/latest/developerguide/blockchain-templates-ethereum.html)




