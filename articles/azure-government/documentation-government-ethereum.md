# Azure Government Blockchain Quickstart Guide

The Ethereum Consortium Network in the Azure Government Marketplace is a blockchain framework implementation that allows developers to easily build a blockchain consensus network using a virtualized infrastructure layer.  


## Getting Started

The Marketplace in Azure Government contains a Resource Template for developers to easily configure the details of the Ethereum blockchain network topology.  Azure Government uses the same underlying technologies as commercial Azure, enabling you to use the development tools you’re already familiar with.
 
This quickstart guide will help you get started implementing an Ethereum Consortium blockchain network on Azure Government.  Developers also have the option to create Hyperledger Fabric, Quorum, and Corda blockchain networks.  Quickstart guides for these platforms will also be posted.


## Prerequisites
Before configuring the sample, you must have the following
* An active Azure Government subscription.  If you don't have an Azure Government subscription, create a free account before you begin.
* Access to an Ubuntu terminal to execute CLI bash.  If on Windows 10, check out the Linux subsystem for Windows: https://msdn.microsoft.com/en-us/commandline/wsl/about

 

# Background on Ethereum

This Azure Government Marketplace template offering generates all of the necessary compute, storage, and networking components to create a private ethereum network on which you can form a shared global ledger state and deploy smart contracts and transactions amongst participating members.

The template configures shared transaction nodes and mining nodes.  All nodes contain an installation of the Golang Ethereum client software (geth).  User applications will interact with the transaction node through an API like web3.js.  The mining nodes are protected by subnet and are not accessible through the public internet.  The mining nodes record all the new transactions submitted to the network while securing the ledger through solving the ETHash proof-of-work algorithm.

A topological view of the network architecture is available below:
 
[picture]


## Provision an Ethereum Consortium Blockchain

1. Log in to the Azure Government Portal.  
1. Click the "New" button in the top left corner of the portal.  
1. Select "Blockchain" from the list of Azure Marketplace offerings.  
  1. Pick the option for "Ethereum Consortium Blockchain".
 
 
Once selected, it is time to configure the network topology and basic settings.  The Template Deployment will walk you through configuring the network.  The deployment flow is ordered into three blades: Basics, Network configuration, and Ethereum configuration.
 
*1. Under the first blade, 'Basics':*
  * Specify values for Resource prefix, VM username, and Authentication type (using  a password - or SSH pubkey for greater security).
    * For Resource group, use the one configured at the start of the guide, or select 'Create new' and specify the value for the parameter name.
    * For Location, select the geographical location in which you wish to host your virtual machines and network to be hosted.
 
 
*2. Under the 'Network size and performance' blade:*
  *	Select the number of Consortium Network Members who will be participating in blockchain consensus.
    * Select the number of mining nodes per each consortium member as meets your security requirements
      *	For a test-run, the default configuration options for storage performance and replication should be fine
    *	Select the number of transaction nodes which users may interact with to submit transactions to the network.
      * Again, for a test-run, the default configuration options for storage performance and replication should be fine
      * A sample snippet is shown below:

 

*3. Under the ‘Ethereum Settings’ blade, we are able to go deeper into the network’s configuration settings.*
  * The ‘Network ID’ setting should be changed a large, nine-digit number to prevent network collusion
  * The ‘Custom Genesis Block’ setting allows us to edit the default genesis JSON file
    1. By editing, the genesis we may predefine address funding (alloc), play around with the Proof-of-Work settings (mixhash, nonce, difficulty), etc.
    1. Nodes in a blockchain network will only pair and synchronize if they both have the same genesis block.
    1. The default genesis file is seen below:
```
{
  "alloc": {
    "f94467d022673a8efb7153dd7772d94f2ded94bf": {
      "balance": "1000000000000000000000000000000"
    }
  },
  "config": {
    "homesteadBlock": 0,
    "chainID": 888345,
    "eip155Block": 0,
    "eip158Block": 0
  },
  "nonce": "0x0000000000000042",
  "difficulty": "0x9999",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "timestamp": "0x00",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "extraData": "0x11bbe8db4e347b4e8c937c1c8370e4b5ed33adb3db69cbdb7a38e1e50b1b82fa",
  "gasLimit": "0x4c4b40"
}
```
  * The account password setting will be used secure the gethadmin account, while the private key passphrase will be used to generate the private key

4. In the next blade, you will see a summary of the inputs specified.  Hit 'OK' to move on to the final blade
 
5. Select 'Create' to deploy your newly configured Ethereum Consortium network.  The resources may take a few minutes to finish deployment.



## Accessing the Nodes
 
1. Once the deployment has been completed, you should be able to access an overview of the resources within the Resource Group.  In the Resource Group blade, click on the 'Deployments' tab (as shown below) 
1. Click on the deployment prepended with the string 'microsoft-azure-blockchain' in the list and look at the details.

 
  

[pic]
 

 


3. Under Outputs, look for the values "ADMIN-SITE”, "ETHEREUM-RPC-ENDPOINT", and "SSH-TO-FIRST-TX-NODE" as shown below.
  * The "SSH-TO-FIRST-TXNODE" gives you a pre-assembled Unix command with all the required parameters to connect to the first VM in your network. 
  * You can now SSH into the VM for each txnode with your admin username and password/SSH key.  
    * Open a fresh terminal on Bash on Ubuntu on Windows and paste the command into the terminal
    * The first TX node is accessed by the default command, additional tx nodes may be accessed by adding to the port number in the initial command (i.e: “-p 3001”)

 


## Creating Account Addresses

To generate a new wallet address, enter the command: “geth account new”
* Enter a unique passphrase to generate randomness required for the seed which the private key.  
*	Important: If you forget your passphrase, there is no way to regain access to the wallet
*	To view a list of accounts, enter the command “geth account list”
*	Keys are stored in the --datadir path (default: ""/home/gethadmin/.ethereum"".  To access the keyfiles, enter the commands:
	cd ~/.ethereum
	cd keystore
	ls
	And use the cat command on the keys to explicate them



## Funding the Addresses

1. Copy and paste the URL for the “ADMIN-SITE” into your Web Browser
* This will give you a snapshot overview of your newly configured Consortium Network.  You will be able to see details on each deployed node, including ‘Hostname’, ‘Participant ID’, ‘Peer Count’, and the ‘Latest Block Number Mined’ (See Below)
  * For testing purposes, you will also be able to make an update to the ledger to fund account addresses with ether.  
	* Fund an address generated in the previous section with 1000 ether by copying and pasting the address into the input form at the bottom of the page.

 

An easy way to play around with wallet addresses with a graphic user interface is by using the MetaMask browser extension in Edge or Chrome.
* The MetaMask browser extension is an SSO solution for web3 – and may be downloaded here (Link.)
* To connect Metamask to your ARM-configured ethereum network, click on the Metamask Fox icon in your browser toolbar, navigate to network settings, click ‘Custom RPC’, and enter the “Ethereum-RPC-Endpoint” listed in the RG Outputs (as described in the previous ‘Accessing the Nodes’ section).





## Checking for Peers

Geth finds peers through something called the discovery protocol.  In the discovery protocol, nodes are gossipping with each other to find out about other nodes on the network. In order to get going initially, geth uses a set of bootstrap nodes whose endpoints are recorded in the source code.

To boot up the web3.js Javascript console, Run:
```
 $ geth attach 
 ```


To check for network connectivity, and peers, enter:
 ```
> net.listening
 ```
[This should return ‘True’ if gossip protocol is active]

```
> net.peerCount
```
[This returns number of peer-nodes]

## Deploying a Smart-Contract
 
Now that we have ssh’d into the node, generated a wallet address, and funded it with some ether, we can try deploying a basic smart contract on our consortium blockchain network.

Smart Contracts are generally written in a language similar to JavaScript - called Solidity (Link.)  To write and compile Solidity, we may leverage traditional options like vim and the solc CLI compiler (Link.) or the VS Code extension for Solidity (Link.) https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity#review-details.  

However, for ease of use, we will be using Remix in this guide, an Integrated Developer Environment (IDE) for Solidity that runs in the browser.  An advantage of using Remix is that it provides an editor and also generates code to deploy the contract.

* Navigate to the Remix URL (https://remix.ethereum.org) within the browser where the MetaMask extension was deployed
* Try running the template file, ballot.sol within the Injected Web3 environment like so: 

 



* In the ‘At Address’ field, enter the contract address which you prefunded with ether in the previous step, and then hit create.  
  * If the contract creation transaction is successful, a message will appear in the console below the text editor in Remix.  
* To double check that the contract transaction has been successfully mined, we may quickly navigate back to the ssh’d geth terminal and enter the command “cat geth.log” on a node to check the ledger through the terminal.
  


Congrats!  You have successfully created your own Ethereum Consortium Network and deployed your first smart-contract to the blockchain.

For further resources related to the programming language Solidity, check out (Link.)

For further resources related to blockchain at Microsoft, check out (Link.)

Feel free to post any questions or comments in the forums below.
