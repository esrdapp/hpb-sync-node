# hpb-sync-node


If you are developing DApps for the HPB chain, or you wish to understand more about how the HPB blockchain is developed, you can run a sync node.

## What is a Sync Node? ##

![HPB Chain](https://raw.githubusercontent.com/adwen2010/other/master/node4.png)


- **Bnode** = static boot node - a node that all nodes must be connected to when starting up, to allow the nodes to eventually discover Cnodes and HNodes.
- **Snode** = sync node (this github) - only used to synchronize the database and does not participate in elections and block generation.
- **Cnode** = Candidate node - Also known as "pre-node" - can go on to become a High Performance node.
- **Hnode** = High-performance node! - Elected from candidate nodes and are the nodes that are used to generate blocks.

In terms of HPB node communication, it falls into two categories:

1.  **Neighbor nodes** - Neighbor nodes refer to nodes that have passed udp handshake communication, and this handshake is used to detect whether the peer node is online.
2.  **Peer nodes** - A peer is a node that has already passed the communication handshake in the network (only neighbor nodes can become peer nodes). The communication handshake is to confirm the legitimacy of the connected node. The peer nodes are mutual. What this means is that node A is the peer of node B, and node B will also be the peer of node A. In a P2P network, every newly accessed node has a possible peer.

The beauty of a decentralized blockchain is that there is no central server that manages nodes, therefore in order for a node to "discover" other nodes, they must first connect to a static boot node (Bnode) with a fixed configuration. The static boot node will hold a record of all known Cnodes and Hnodes on the blockchain. From that point onwards they will discover network peers on the HPB blockchain. From this, the nodes can build their own peer lists.

For a complete list of Candidtate nodes (cnodes, also known as "pre-nodes") and High Performance nodes (hnodes), you can visit this page:
https://hpbscan.org/nodes


## running a HPB sync node ##


The following readme guide explains how to run a syncnode in the Linux environment. 

If you have Windows, you can run a virtualised version of Linux using WSL2
https://docs.microsoft.com/en-us/windows/wsl/install-win10


Firstly enure you have node installed - https://nodejs.org/en/download/
Also ensure you have git installed - https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

Switch to super user

```
sudo su
```

Now create a suitable folder

```
cd \
mkdir /home/syncnode
cd /home/syncnode
```


Create a suitable folder to run the packages and type in
```
git clone https://github.com/hpb-project/hpb-release
```
```
cd hpb-release
```

The two areas of focus are the bin folder (houses all the HPB releases) and the config folder which is home the the configuration file, called gensis.json

Whilst still inside the hpb-release folder, create a new folder called ghpb-bin

```
mkdir ghpb-bin
```

Now copy the latest release of hpb (at the time of writing it was v1.0.8.0) and the configuration file into this new folder.

```
cp ./bin/ghpb-v1.0.8.0.tar.gz ./ghpb-bin/
cp ./config/gensis.json ./ghpb-bin/
```

We now switch to ghpb-bin folder so that we can unzip/extract the tar.gz file

```
cd ghpb-bin
tar xzvf ghpb-v1.0.8.0.tar.gz
```

We now have a new folder inside the ghpb-bin folder, named the same as the HPB release file. It should include a ghpb file and an iperf3 file
Firstly, change the new folder permissions

```
sudo chmod +x ghpb-v1.0.8.0 -R
```

Next we need to copy the new files from the ghpb-v1.0.8.0 folder into the current ghp-bin folder.

```
cp -a ./ghpb-v1.0.8.0/. .
```

The next thing to do is to actually initialize the syncnode:

```
sudo ./ghpb --datadir node/data init gensis.json
```

If successful, you should see something similar to this output:

```
INFO [09-13|14:25:54]  HPB : Create New HpbConfig object
INFO [09-13|14:25:54]  HPB : Set global gas cap                cap=25000000
INFO [09-13|14:25:54]  HPB : Start Node with                   Archived block =-1
INFO [09-13|14:25:54]  HPB : Allocated cache and file handles  database=/home/syncnode/hpb-release/ghpb-bin/node/data/ghpb/chaindata cache=16 handles=16
INFO [09-13|14:25:54]  HPB : Writing custom genesis block
INFO [09-13|14:25:54]  HPB : Successfully wrote genesis state  database=chaindata                                                    hash=9c3704…f39966
```

You should also see a new "node" folder in the ghp-bin folder:

```
root@DELL-PC:/home/syncnode/hpb-release/ghpb-bin# ls -l
total 47396
-rw-r--r-- 1 root root       1754 Sep 13 14:04 gensis.json
-rwxr-xr-x 1 root root   32604976 Aug 21 16:01 ghpb
drwxrwxr-x 2 1001 docker     4096 Aug 21 16:01 ghpb-bin
drwxrwxr-x 2 1001 docker     4096 Aug 21 16:01 ghpb-v1.0.8.0
-rw-r--r-- 1 root root   15429496 Sep 13 14:04 ghpb-v1.0.8.0.tar.gz
-rwxr-xr-x 1 root root     476336 Aug 21 16:01 iperf3
drwx------ 3 root root       4096 Sep 13 14:25 node
```

Inside this node folder will be a data folder, and inside this will be a ghpb file. Navigate to this file:

```
cd node/data
```

Now we make a keystore folder that will allows us to interact with the node.

```
mkdir keystore
```

You can either import an existing keystore, or simply create a new wallet and generate a keystore. The easiest way to create a new one is by using myetherwallet.
https://www.myetherwallet.com/wallet/create/software?type=keystore

(Remember, your keystore information will be the same for the wallet you create, regardless of which EVM chain you use, hence the reason why we can use MyEtherWallet to generate a keystore)

Once you have created a keystore you can download it. It will look something like this:
UTC--2021-09-13T13-48-08.374Z--1890927b993fee8c7f1ce6737ccfb5ed55ca881b

You now need to copy this keystore file into your /ghpb-bin/node/data/keystore/ folder, so you'll need to do something like this:

```
cp ./[your computers download folder path]/UTC--2021-09-13T13-48-08.374Z--1890927b993fee8c7f1ce6737ccfb5ed55ca881b ./keystore/
```
(Note: If you do not have permission to enter this directory, come back to the hpb-release folder and enter the command chmod 777 ghpb-bin -R and then try again)

Once you've moved the keystore file, ensure you are in the ghpb-bin folder:

```
/hpb-release/ghpb-bin
```

now run the following command:

```
./ghpb --datadir node/data account new
```

You will be prompted for your keysotre password (Either your existing one, or the one you used to create your keystore in MyEtherWallet)

Once confirmed, you should see your new address appear in curly braces on screen, e.g. Address: {b741bb9fbe2xcabbdeed16d5561e400c5273ec5035}

Make a note of this address somewhere!

You are now ready to launch your syncnode with the following command:

```
sudo ./ghpb --datadir node/data --networkid 100 --port 3004 --syncmode full --nodetype synnode console;
```

This will take quite some time! Many hours or possibly even days, depending on when this readme is being read! HPB is currently running at block 11,940,064 at the time of writing (15:11pm - 13/09/2021)
sp you will need to be patient for the node to fully sync with the blockchain. You can check what the current block height is here:
https://hpbscan.org/blocks

When the syncnode eventually does sync, then you will be able to interact with it. You'll see a message saying  "Welcome to the GHPB JavaScript console!"

It's important to note that ghpb is a clone of geth (Go Ethereum) - for more information on interacting with your syncnode you, can find commands here:
https://geth.ethereum.org/docs/interface/command-line-options

For every command you see in geth that begins with geth, youwould substitute this for ghpb. For example:

```
sudo ./geth attach http://127.0.0.1:8545
```
would in fact become...
```
sudo ./ghpb attach http://127.0.0.1:8545
```




