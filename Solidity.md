# _**ABI (Application Binary Interface)**_ - what is it?

# _**EVM (Ethereum Virtual Machine)**_

# **_Events_**
are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.
```
// declare the event
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
  // fire an event to let the app know the function was called:
  emit IntegersAdded(_x, _y, result);
  return result;
}
```
Your app front-end could then listen for the event. A JavaScript implementation would look something like:
```
YourContract.IntegersAdded(function(error, result) {
  // do something with the result
})
```
# msg.sender

In Solidity, there are certain global variables that are available to all functions. One of these is `msg.sender`, which refers to the [[address]] of the person (or smart contract) who called the current function.

# two locations you can store variables — in `storage` and in `memory`.

**_Storage_** refers to variables stored permanently on the blockchain. **_Memory_** variables are temporary, and are erased between external function calls to your contract. Think of it like your computer's hard disk vs RAM.

State variables (variables declared outside of functions) are by default `storage` and written permanently to the blockchain, while variables declared inside functions are `memory` and will disappear when the function call ends.
# Отличия Solidity от других языков
## Неизменяемость
После того, как контракт был создан, он не может быть изменён, поэтому важно предусмотреть функции для редактирования ключевых частей контракта.
[[Gas — the fuel Ethereum DApps run on]]
## [[Gas — the fuel Ethereum DApps run on]]
## [[Ether --  the value of Etherium]]
# OpenZeppelin's `Ownable` contract

Below is the `Ownable` contract taken from the **_OpenZeppelin_** Solidity library. OpenZeppelin is a library of secure and community-vetted smart contracts that you can use in your own DApps.
```
pragma solidity >=0.5.0 <0.6.0;
/**
* @title Ownable
* @dev The Ownable contract has an owner address, and provides basic authorization control
* functions, this simplifies the implementation of "user permissions".
*/
contract Ownable {
address private _owner;
  
event OwnershipTransferred(
address indexed previousOwner,
address indexed newOwner
);
  
/**
* @dev The Ownable constructor sets the original `owner` of the contract to the sender
* account.
*/
constructor() internal {
_owner = msg.sender;
emit OwnershipTransferred(address(0), _owner);
}
  
/**
* @return the address of the owner.
*/
function owner() public view returns(address) {
return _owner;
}
  
/**
* @dev Throws if called by any account other than the owner.
*/
modifier onlyOwner() {
require(isOwner());
_;
}
  
/**
* @return true if `msg.sender` is the owner of the contract.
*/
function isOwner() public view returns(bool) {
return msg.sender == _owner;
}
  
/**
* @dev Allows the current owner to relinquish control of the contract.
* @notice Renouncing to ownership will leave the contract without an owner.
* It will not be possible to call the functions with the `onlyOwner`
* modifier anymore.
*/
function renounceOwnership() public onlyOwner {
emit OwnershipTransferred(_owner, address(0));
_owner = address(0);
}
  
/**
* @dev Allows the current owner to transfer control of the contract to a newOwner.
* @param newOwner The address to transfer ownership to.
*/
function transferOwnership(address newOwner) public onlyOwner {
_transferOwnership(newOwner);
}
  
/**
* @dev Transfers control of the contract to a newOwner.
* @param newOwner The address to transfer ownership to.
*/
function _transferOwnership(address newOwner) internal {
require(newOwner != address(0));
emit OwnershipTransferred(_owner, newOwner);
_owner = newOwner;
}
}```
