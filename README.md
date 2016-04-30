# Initialize Ethereum Private Network

Start a basic Ethereum private network in minutes!

A script for initializing an Ethereum private network for testing and development.

### Prerequisites

These scripts were developed for two freshly created Xubuntu 16.04LTS virtual machines running in Qemu/KVM.

They should work without problem in all recent Debian based VMs in any hypervisor, but this has not been tested.

### Getting it

Make sure you know how to clone with git, then do :

    git clone git@github.com:FleetingClouds/InitializeEthereumPrivateNetwork.git
    cd InitializeEthereumPrivateNetwork
    
### Running it

The same command builds either a "root" node or a "client" node :

    ./initializeEthereumTestNode.sh
    
#### Persistence

When you have answered the prompts, it'll do its work.  It keeps your prompts in a file, `${HOME}/.userVars.sh` that looks like this :

    export NETWORK_ID='';
    export ACCOUNT_PASSWORD='';
    export NETWORK_ROOT_IP='';
    export NETWORK_ROOT_UID='';
    export ROOT_PROJECT_DIR='';
    export PROJECT_DIR='';
    export NODE_INFO_FILE='';
    export DROP_DAGS='';
    export DROP_BLOCKCHAIN='';
    export DROP_CLIENTFILES='';
    export NODE_TYPE='';

The script tries to be *idempotent*, so it should not matter how often you run it.  It remembers your settings for you, so it serves not only to initialize a new network, but as a reminder of how you set it up in the first place.

#### Wizard

If you have inadequate **available** memory you'll see ...

    Memory - 379MB!

      According to 'free -m' your available memory is 
      less than the viable minimum :: 1100MB.

... otherwise you'll see ...

    This 'wizard' makes it easy to set up an Etheruem private network between virtual machines.
    It's intended as a quick start for developer experimentation

    Please choose <r>oot node, <c>lient node or <q>uit. (r/c/q) ::  

The choice makes no sense for a *peer to peer* system, but it's there for convenience.  It just means that the first node created acts as a source of configuration specifics for subsequent nodes. The script takes care of the data ecchange, so you won't have to.
     
####  First (root) node

If you type 'r', 'y' & 'n', without <return>, you'll see :

    Please choose <r>oot node, <c>lient node or <q>uit. (r/c/q) ::  r
    Get parameters and generate a new ROOT node now? (y/n)::  y
      
    Requesting parameters for ROOT set up.

        1  Network Identification number (--networkid) :  
        2  Accounts password :  
        3  Project directory on THIS node :  
        4  Delete DAG file (y/n) :  
        5  Delete block chain (y/n) : 
        6  Delete other client files (y/n) : 


    Is this correct? (y/n/q) ::  n

So these are :

1. *--networkid* : see the geth command line documentation
2. *Password* : this is **NOT** for production use, all accounts will have the same passwords.  They'll be visible!
3. *Directory* :  to help understand where stuff really goes I **don't** use the default directory: `~/.ethereum`
4. *DAG* : Whether or not you want to delete it.
5. *chaindata file* : Whether or not you want to delete it.
6. *everything else* : Whether or not you want to delete it.

Typically I do :

    The number you will use to identify your network :: 7028
    The password to use when creating accounts :: plokplok
    The directory for geth's working files on THIS node :: .dappNet
    You want to delete the DAG file (y/n) :: n
    You want to delete the block chain file (y/n) :: n
    You want to delete all the other client files (y/n) :: n
    
    Is this correct? (y/n/q) ::  y
  
The script then does these things for you :

1. Installs`ethereum` from `ppa:ethereum/ethereum-dev`
2. Prepares work directories where you specified, eg `/home/you/.dappNet`
3. Writes the accounts password to `~/.ssh/.EthereumAccountsPassword`
4. Tries to get your coin base account number if available
5. Copies an unconfigured Genesis file to the workspace, if there's not one already
6. Updates it with preallocation to your coin base account, if needed
7. Initializes you private Block Chain's foundation block, unless done already
8. Copies a mining initialization script to your workspace (from `./js/initialTrivialMiningScript.js`)
9. Tries to read your coin base account balance
10. Mines a few blocks if its balance is low (from previous sessions)

When th's done it shows a *help* sheet :

          * * * Setup has Finished * * *  

     You are ready to start mining. Run the following command : 

    geth --datadir /home/you/.dappNet/geth --verbosity 3 --maxpeers 5 --networkid 7028 --nodiscover console 2>> /home/you/.dappNet/prvWhsmn.log


     ~~ To view accumulated ether, enter      > web3.fromWei(eth.getBalance(eth.accounts[0]), "ether") 

     ~~ To continue mining                    > miner.start(2) 

     ~~ Then to pause mining                  > miner.stop() 

     ~~ Copying a JavaScript example of transaction monitoring.

     ~~ To have your root node process transactions automatically run, it with this command . . .
    geth --datadir /home/you/.dappNet/geth --jspath /home/you/.dappNet/scripts --preload "MineIfWorkToBeDone.js" --verbosity 3 --maxpeers 5 --networkid 7028 --nodiscover console 2>> /home/you/.dappNet/prvWhsmn.log
        When there are transactions in need of processing you will see . . .  
         * ==  Pending transactions! Mining...  == *    
      . . . and once all transactions have been processed it will report . . .    
         * ==  No transactions! Mining stopped.  == *   

     ~~ To attach from another local terminal session, use :
    geth --datadir /home/you/.dappNet/geth --networkid 7028 attach ipc://home/you/.dappNet/geth/geth.ipc



     ~~ Client nodes will need these four data elements in order to connect :
        ~ The IP address of your root node machine                      :: 192.168.122.142
        ~ The directory for geth's working files ON THE ROOT NODE       :: .dappNet
        ~ The SSH user name of the root node machine's Ethereum account :: you
        ~ The SSH password of the root node machine's Ethereum account  :: ????????????

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    Done.


####  Subsequent (client) nodes









