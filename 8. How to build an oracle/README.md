# Calling other contracts
One of the things the caller smart contract does is to interact with the oracle. 

Let's see how you can do this.
For the caller smart contract to interact with the oracle, you must provide it with the following bits of information:
 - The address of the oracle smart contract
 - The signature of the function you want to call

## Calling the Oracle Contract

For the caller contract to interact with the oracle, you must first define something called an interface.

Interfaces are somehow similar to contracts, but they only declare functions. In other words, an interface can't:

define state variables,
constructors,
or inherit from other contracts.
You can think of an interface as of an ABI. Since they're used to allow different contracts to interact with each other, all functions must be external

Let's look at a simple example. Suppose there's a contract called FastFood that looks something like the following:

```js
pragma solidity 0.5.0;

contract FastFood {
  function makeSandwich(string calldata _fillingA, 		 string calldata _fillingB) external {
    //Make the sandwich
  }
}
```
This very simple contract implements a function that "makes" a sandwich. If you know the address of the FastFood contract and the signature of the makeSandwich, then you can call it.

Continuing with our example, let's say you want to write a contract called PrepareLunch that calls the makeSandwich function, passing the list of ingredients such as "sliced ham" and "pickled veggies". 

To make it so that the PrepareLunch smart contract can call the makeSandwich function, would look like:

```js
pragma solidity 0.5.0;
import "./FastFoodInterface.sol";

contract PrepareLunch {

  FastFoodInterface private fastFoodInstance;

  function instantiateFastFoodContract (address _address) public {
    fastFoodInstance = FastFoodInterface(_address);
    fastFoodInstance.makeSandwich("sliced ham", "pickled veggies");
  }
}
```
------------

To initiate an ETH price update, the smart contract should call the getLatestEthPrice function of the oracle. Now, due to its asynchronous nature, there's no way the getLatestEthPrice function can return this bit of information. What it does return instead, is a unique id for every request. Then, the oracle goes ahead and fetches the ETH price from the Binance API and executes a callback function exposed by the caller contract. Lastly, the callback function updates the ETH price in the caller contract.

As mentioned in the previous chapter, calling the Binance public API is an asynchronous operation. Thus, the caller smart contract must provide a callback function which the oracle should call at a later time, namely when the ETH price is fetched.

Here's how the callback function works:

First, you would want to make sure that the function can only be called for a valid id. For that, you'll use a require statement.

Simply put, a require statement throws an error and stops the execution of the function if a condition is false.

Let's look at an example from the Solidity official documentation:
```js
require(msg.sender == chairperson, "Only chairperson can give right to vote.");
```
The first parameter evaluates to true or false. If it's false, the function execution will stop and the smart contract will throw an error- "Only chairperson can give right to vote."

Once you know that the id is valid, you can go ahead and remove it from the myRequests mapping.

Lastly, your function should fire an event to let the front-end know the price was successfully updated.

!!! You must make sure that only the oracle contract is allowed to call it.


