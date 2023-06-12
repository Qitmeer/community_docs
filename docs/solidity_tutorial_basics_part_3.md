# Solidity Tutorial - Basics (Part 3)

In the previous tutorial, we looked at the different types of variables and how to assign them. In this section we will look into an essential component in smart contracts (and generally most programming languages) - Functions.

## Functions

Functions are reusable bits of code that can be called multiple times. In Solidity, they can be declared using the `function` keyword and the following syntax:

```solidity
function <function-name>(<list of parameters and type>) <visibility> <mutability> <modifier> returns (<type of return value>) {
<embed logic here>
}
```

## Function Visibility

When creating functions, you need to specify who can directly call functions inside of that smart contract by including its function visibility.

There are 4 types of visibility:

- `public`
- `private`
- `internal`
- `external`

### Public

As the name suggests, [public functions](https://docs.soliditylang.org/en/latest/contracts.html#visibility-and-getters) with the `public` keyword will be accessible to all users and contracts. State variables can also be assigned the `public` keyword, and the compiler will automatically create a getter function.

```solidity
contract Parent {

// We can make a state variable public and can access it using the getter function created by the compiler
string public publicString = "This is a public function!";

// We create a public function that returns a string
function publicFunction() public view returns (string memory) {
		return publicString;
}

// functions inside of our contract can call this public function
function functionOne() public view returns (string memory) {
		return publicFunction();
}

}

// using the "is" keyword we can use this to inherit functions
contract Child is Parent {

// functions inherited from another contract can also call this function
function functionTwo() public view returns (string memory) {
		return publicFunction();
}

}
```

We can also call `publicFunction()` from within another smart contract if contract `Parent` is deployed on the blockchain:

```solidity

// We first need to declare an interface to interact with another contract
interface ParentInterface {
		
		// functions must be declared as external in the interface even if they are public in the contract they are being called from
    function publicFunction() external view returns (string memory);
}

contract AnotherContract {

		// address 0xf8e81D47203A594245E36C48e151709F0C19fBe8 is where our Parent contract is stored (for example sake)
		address parentAddress = 0xf8e81D47203A594245E36C48e151709F0C19fBe8;

    function functionTwo() external view returns (string memory) {
		    return ParentInterface(parentAddress).publicFunction();
    }
}

// functionTwo() => string : "This is a public function!"
```

### Private

Functions and variables labelled as `private` can only be accessed by the contract that has created the function. They cannot be passed on through inherited contracts or other contracts deployed on the blockchain. 

```solidity
contract Parent {

// This does not have to be declared private for use in a private function
string private privateString = "This is a private function!";

function privateFunction() private view returns (string memory) {
		return privateString;
}

// This function will be able to call privateFunction()
function functionOne() public view returns (string memory) {
		return privateFunction();
}
}

// Inherited contracts will not be able to inherit private functions
contract Child is Parent {

// This function will not be able to call privateFunction() and produce an error
function functionTwo() public view returns (string memory) {
		return privateFunction(); // <= error produced here
}
}
```

### Internal

`internal` functions and variables are similar to `private` except they can be inherited by other contracts. However they cannot be called from other contracts using an interface:

```solidity
contract Parent {

// According to Solidity docs, this is the default visibility level for state variables therefore internal keyword is not neccesary
string internal internalString = "This is a internal function!";

function internalFunction() internal view returns (string memory) {
		return internalString;
}

// This will work
function functionOne() public view returns (string memory) {
		return internalFunction();
}
}

// Inherited contracts will be able to call internal functions
contract Child is Parent {

// This will also work
function functionTwo() public view returns (string memory) {
		return internalFunction(); 
}
}
```

### External

`external` functions can only be called on the outside by an external user or contract. Variables cannot have the `external` visibility. 

```solidity
contract Parent {

// Variables cannot be declared external
string externalString = "This is an external function!";

function externalFunction() external view returns (string memory) {
		return externalString;
}

// This will not work
function functionOne() public view returns (string memory) {
		return externalFunction();
}
}

// Inherited contracts will also NOT be able to call external functions
contract Child is Parent {

// This will NOT work
function functionTwo() public view returns (string memory) {
		return externalFunction(); 
}
}
```

## State Mutability

In some of our examples we have repeatedly seen the keywords `view` and `pure` used but did not discuss what these terms mean. When defining a function, we can declare whether the function is going to alter the state of the blockchain. If the function simply reads the state, we can declare the function with the `view` keyword. If the function does not read or write to the state, then we can declare this function as `pure` . 

Functions that are declared `view` or `pure` will not cost any gas if they are called externally as any computation can be done through the local node. However, if these functions are called internally by other functions, then this will incur a gas fee. 

## Return Statement

For a function to return something, you must specify the the `returns` keyword in the function declaration and include the type that is going to be returned inside brackets. You would then embed the `return` keyword before the value you would want to return.

For instance, if you wanted to create a function to return a number:

```solidity
uint number = 10;

// we first use the returns key word and specify the type (in this case its uint)
function retrieveNumber() public view returns (uint) {
		// add the return keyword before the value you want to return
		return number;
}
```

If you want to return something that is of reference type (e.g. array, string), then you must also specify the data location (most of the times this will be `memory` ): 

```solidity
string myString = "Hello World!";

// The memory keyword must be used here as string is a reference type
function retrieveString() public view returns (string memory) {
		return myString;
}
```

If we want to return multiple values, we need to also include their type into the function declaration: 

```solidity
uint number = 10;
string numAsString = "Ten";

function retrieveNumber() public view returns (uint, string memory) {
		// We add brackets which will return a tuple of our values
		return (number, numAsString);
}

// We can retrieve our values from our function like this
function exampleOne() public view {
		(uint a, string memory b) = retrieveNumber();
}

// or like this
function exampleTwo() public view {
		uint a;
		string memory b;

		(a,b) = retrieve();
}
```

## Modifiers

Modifiers are special types of functions that can be “added” to modify the behaviour of an existing function. One common use case for modifiers is to ensure certain conditions are met before the function being called can be invoked e.g. checking to see if user is admin or has required permissions. Whilst modifiers may not be necessary and any logic inside the modifier can be simply embedded into a function, its use makes the code more readable for other developers.  

```solidity
uint256 number;
address owner = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4;

// We create a modifier that will only allow the specified address to call a function
modifier onlyOwner() {
		require(msg.sender == owner, "Permission Denied - Not the Owner!");
		// The "_;" is a special syntax that determines when you want the modifier to execute
	  _;
}

// We append the onlyOwner modifier to our function which will be executed first before storeNumber
function storeNumber(uint num) public onlyOwner {
		number = num;
}

// If we attempt to call the function using a different address than variable "owner", it will produce an error and revert the state
```

The location of `_;` is important as it instructs when the code inside the modifier should be run. Anything **above** the `_;` is ran **before** the function (which the modifier has been appended to) is itself executed. Any code that is **below** `_;` will be executed after the **after** the function has finished executing. 

We can use the previous example to demonstrate this concept:

```solidity

/*
We will be calling storeNumber using address 0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2

Depending on where the _; is placed, will determine whether we can call storeNumber
*/

uint256 number;
address owner = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4;

modifier onlyOwner() {
		// This line is going to be executed first before storeNumber
		// Since the check comes first, we will not be able to call storeNumber
		require(msg.sender == owner, "Permission Denied - Not the Owner!");
	  _;
}

function storeNumber(uint num) public onlyOwner {
		number = num;
		// Lets change the address of the owner
		owner = 0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2
}

// In this case we will not be able to call storeNumber as the require statment is executed first before we can change the owner variable
```

```solidity
/*
By placing the _; above the require statement we can change the address before running the require function
*/

uint256 number;
address owner = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4;

modifier onlyOwner() {
		// we can now change the address of the owner before the require statement
		_;
		require(msg.sender == owner, "Permission Denied - Not the Owner!");
}

function storeNumber(uint num) public onlyOwner {
		number = num;
		owner = 0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2
}

// In this example we will now be able call storeNumber using address 0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2
```

Since the modifier is a function, we can also include function parameters just like normal functions. We will add a modifier to our original code with the condition that `number > 10`. 

```solidity
uint256 number;
address owner = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4;

modifier onlyOwner() {
		require(msg.sender == owner, "Permission Denied - Not the Owner!");
		// The "_;" is a special syntax that determines when you want the modifier to execute
	  _;
}

modifier greaterThanTen(uint _number) {
		require(_number > 10, "Number must be greater than 10!");
    _;
}

function storeNumber(uint num) public onlyOwner greaterThanTen(num) {
		number = num;
}

// calling storeNumber(5) will produce an error and revert

```
