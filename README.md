# Interacting with ERC20 contract in python3 and web3.py

### Installing dependencies
After installing jsonschema==3.2.0 restart the kernel


```python
! pip install  web3
```


```python
# IMPORTANT: after installing jsonschema==3.2.0 restart the kernel runtime
!pip install --force-reinstall jsonschema==3.2.0
```


```python
from web3 import Web3
import requests
```


```python
adapter = requests.adapters.HTTPAdapter(pool_connections=20, pool_maxsize=20)
session = requests.Session()
session.mount('http://', adapter)
session.mount('https://', adapter)
w3 = Web3(Web3.HTTPProvider("https://ropsten.infura.io/v3/9aa3d95b3bc440fa88ea12eaa4456161", session=session))
```


```python
w3.isConnected()
```

## Interacting with existing contract. 
#### In this case custom token (SPToken Contract)


```python
contract_addr = "0x3055cbB4bA30F4b39Aec6Ca584e2f1cae4BD01fA"
ABI = '[{"inputs":[{"internalType":"uint256","name":"initialSupply","type":"uint256"}],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"owner","type":"address"},{"indexed":true,"internalType":"address","name":"spender","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"inputs":[{"internalType":"address","name":"owner","type":"address"},{"internalType":"address","name":"spender","type":"address"}],"name":"allowance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"approve","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"}],"name":"balanceOf","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"decimals","outputs":[{"internalType":"uint8","name":"","type":"uint8"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"subtractedValue","type":"uint256"}],"name":"decreaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"addedValue","type":"uint256"}],"name":"increaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"name","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"symbol","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function"},{"inputs":[],"name":"totalSupply","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transfer","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"sender","type":"address"},{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transferFrom","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"}]'
```


```python
# Connecting with contract
myContract = w3.eth.contract(address=contract_addr, abi=ABI)
```

## Querying token metadata


```python
# Both lines are similar
# get symbol of the token
# print('token name: ', myContract.functions.symbol().call())
print('token name: ',myContract.caller.symbol())
# get decimals
decimals = myContract.caller.decimals()
print('Decimals: ',decimals)
DECIMALS = 10 ** decimals
# get total supply
print('Total Supply: ',myContract.caller.totalSupply() // DECIMALS)
```

## Query account balances


```python
myAddr = 'YOUR WALLET ADDRESS i.e PUBLIC KEY'
raw_balance = myContract.caller.balanceOf(myAddr)
print("Balance is : {0} SP".format(raw_balance // DECIMALS))
```

## Send Transaction


```python
myAddr = 'YOUR WALLET ADDRESS i.e PUBLIC KEY'
Private_Key  =  'YOUR PRIVATE KEY'
receiver = 'RECEIVER WALLET ADDRESS i.e PUBLIC KEY'
```


```python
# 100 SP Tokens
transferAmount = 100*DECIMALS
# chainId is very important 
txn = myContract.functions.transfer(receiver, transferAmount).buildTransaction({'chainId': 3, 'gas':70000, 'nonce': w3.eth.getTransactionCount(myAddr)})
```


```python
signed_txn = w3.eth.account.signTransaction(txn, Private_Key)
```


```python
txn_hash = w3.eth.sendRawTransaction(signed_txn.rawTransaction)
```


```python
print('View transaction on Etherscan (Ropsten Test Net) : https://ropsten.etherscan.io/tx/{0}'.format(str(w3.toHex(txn_hash))))
```
