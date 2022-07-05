## Metamask web3 provider:

Managing user accounts, private keys and connection to Ethereum nodes
Metamask injects web3 provider into the browser in the global Javascript object **web3**
So when developing dapp, check for web3 object and use web3.currentProvider. If not ask them to install Metamask to continue using the DApp
```
//Initialize Web3.js using metamask's default web3 provider (infura)
<body>
<script>
window.addEventListener('load', function() {

  // Checking if Web3 has been injected by the browser (Mist/MetaMask)
  if (typeof web3 !== 'undefined') {
    // Use Mist/MetaMask's provider
    web3js = new Web3(web3.currentProvider);
  } else {
    // Handle the case where the user doesn't have web3. Probably
    // show them a message telling them to install Metamask in
    // order to use our app.
  }

  // Now you can start your app & access web3js freely:
  startApp()

})
</script>
</body>
```
##Using Contract
- You will need the contract address and Application Binary Interface (ABI) 
Above code continued
```
<head>
<script language="javascript" type="text/javascript" src="contract_abi.js"></script>
</head>
<body>
  <script>
      var contract;

      function startApp() {
        var contractAddress = "YOUR_CONTRACT_ADDRESS";
        contract = new web3js.eth.Contract(contractABI, contractAddress);
      }

      window.addEventListener('load', function() {

        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }

        // Now you can start your app & access web3 freely:
        startApp()

      })
    </script>
</body>  
```    

## Calling contract functions

**1. call**

- Runs on local node without creating transactions on blockchain.
- view, pure function
- no need to sign transactions and user won't be prompted
- no gas cost
```
contract.methods.methodname(arg1,arg2).call()
```

**2. send**
- To create transaction, change data
- Not for view and pure
- Costs gas and prompts automatically
```
contract.methods.methodName(arguments)
  .send({ from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155", gas: "3000000" })
```

##Managing current user's logged in information
```
<head>
<script language="javascript" type="text/javascript" src="contract_abi.js"></script>
</head>
<body>
  <script>
      var contract;
      var useAccount;
      function startApp() {
        var contractAddress = "YOUR_CONTRACT_ADDRESS";
        contract = new web3js.eth.Contract(contractABI, contractAddress);
        var accountInterval = setInterval(function(){
          if (web3.eth.accounts[0]!=userAccount){
            userAccount = web3.eth.accounts[0];
            updateUI(); //add anything to update the ui
          }
        },100);
      }

      window.addEventListener('load', function() {

        // Checking if Web3 has been injected by the browser (Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Use Mist/MetaMask's provider
          web3js = new Web3(web3.currentProvider);
        } else {
          // Handle the case where the user doesn't have Metamask installed
          // Probably show them a message prompting them to install Metamask
        }

        // Now you can start your app & access web3 freely:
        startApp()

      })
    </script>
</body>  
```    
## Sending wei to payable methods
```
<contractName>.methods.methodname(<args>)
.send({ from: userAccount, value: web3js.utils.toWei("0.001", "ether") })
.on("receipt",function(){})
.on("error",function(error){console.log(error})
```

## Subscribe to events
```
<contractName>.events.<eventName>()
.on("data",function(event){let returnvalues = event.returnvalues;})
.on("error",console.error)
```

## Indexed events/ Filtering events
Listening to changes for an indexed param's particular value
```
<contractName>.events.<eventName>({filter:{<indexedParam>: <value>})
.on("data",function(event){let returnvalues = event.returnvalues;})
.on("error",console.error)
```

## Querying past events using filters for fromBlock and toBlock
```
contract.getPastEvents(<eventName>,
{fromBlock:0, toBlock:"latest"})
.then(function(events){
  // `events` is an array of `event` objects that we can iterate, like we did above
})
```
