# Solidity Tutorial - Basics (Part 2)

In this section, we’ll be delving deeper into Solidity and covering further concepts such as: variables (add more here)

## Variables

Solidity is a statically typed language meaning that the type of the variable must be explicitly declared before a value can be assigned to it. For instance, if you wanted to store a 256-bit integer into variable `a`, you would write:

```solidity
// Declare an integer
int a;
a = 20;

// or like this
int a = 20;

// or since int and int256 are aliases, you could write:
int256 a = 20;
```

In Solidity variables can be categorised into one of two types:

- **Value Types**
- **Reference Types**

### Value Types

[Value types](https://docs.soliditylang.org/en/latest/types.html#value-types) basic data types whose variables are passed by value when used in function arguments or assignments. This means that when they are assigned or passed as arguments, a copy of the value is created

These types include:

- Boolean
- Signed/Unsigned Integer
- Fixed Point Numbers (although not fully supported by Solidity yet)
- Address (Ethereum)
- Fixed-size Bytes
- Enum

```solidity
// Unsigned Integer
int myInt = -20;

// Integer
uint myUint = 30;

//Boolean
bool myBool = true;

// Etheruem Address
address myAddress = 0x751bc4cd7E77033fBDb9206fF306549d6dD017f8;

// Bytes (can be between bytes1..32)
bytes32 myBytes = "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa";

// Enums (return uint depending on index)
enum Button {
	OFF,
	PENDING,
	ON
}

/*
Returns uint8 depending on the index of which element was selected
OFF - 0
ON - 1
*/

// This would return a uint with value 0
Button myButton = Button.OFF;

// This would return a uint with value 1
Button myButton = Button.PENDING

// This would return a uint with value 2
Button myButton = Button.ON
```

### **Reference Types**

[Reference types](https://docs.soliditylang.org/en/latest/types.html#reference-types) are types that store a reference to the memory location where the data is stored, rather than the data itself. When a reference type is assigned or passed as an argument, only the reference to the memory location is copied, not the actual data.

These types include:

- Array
- String
- Struct
- Mapping

### Fixed Arrays

[Fixed arrays](https://www.tutorialspoint.com/solidity/solidity_arrays.htm) are arrays that store a fixed size collection of elements of the same type.

```solidity
// Declare a fixed array of unsigned integers
uint[5] fixArray = [10,20,30,40,50];

function retrieveElementArray(uint index) public view returns (uint) {
	return fixArray[index];
}

function retrieveFullArray() public view returns (uint[5] memory) {
	return fixArray;
}

/*
retrieveElementArray(0) => uint256 : 10
retrieveElementArray(1) => uint256 : 20
retrieveElementArray(4) => uint256 : 50

Selecting an index outside the stated range will produce an error and revert the EVM to the intial state
retrieveElementArray(10) => error

retrieveFullArray() => uint256[5] : [10,20,30,40,50]
*/
```

- `uint` and `uint256` are exact aliases and behave exactly the same.

In a fixed array, any element not assigned will be set to its default value depending on its type:

```solidity
// Declare a fixed array of unsigned integers
uint[5] fixArray = [10,30,40,50];

function retrieveElementArray(uint index) public view returns (uint) {
	return fixArray[index];
}

function retrieveFullArray() public view returns (uint[5] memory) {
	return fixArray;
}

/*
retrieveElementArray(0) => uint256 : 10
retrieveElementArray(1) => uint256 : 30

As this element hasn't beeen assigned, it will be set to 0 (this is the default value for type uint256)
retrieveElementArray(4) => uint256 : 0

retrieveFullArray() => uint256[5] : [10,30,40,50,0]
*/
```

We can also create temporary arrays stored in memory inside of functions. Unlike arrays in storage, the elements of these arrays would be removed after the execution of the function. 

```solidity
function createFixArray() public pure returns (uint[5] memory) {
	uint[5] memory fixArray;
	fixArray[0] = 10;
	fixArray[1] = 20;
	fixArray[2] = 30;

	return fixArray;
}
```

```solidity
// This won't work
function createFixArray() public pure returns (uint[5] memory) {
	uint[5] memory fixArray;
	fixArray = [10,20,30,40,50]; // <= TypeError: Type uint8[5] memory is not implicitly convertible to expected type uint256[5] memory
	
	return fixArray;
}
```

```solidity
//But this will - array needs to be converted to type uint8
function createFixArray() public pure returns (uint8[5] memory) {
	uint8[5] memory fixArray;
	fixArray = [10,20,30,40,50];
	
	return fixArray;
}
```

### Dynamic Arrays

[Dynamic arrays](https://www.geeksforgeeks.org/dynamic-arrays-and-its-operations-in-solidity/) unlike fixed sized arrays, can be used to store an arbitrary number of elements of the same type.

```solidity
// Declare a dynamic array of unsigned integers
uint[] dynArray = [10,20,30,40,50];

function retrieveElementDynArray(uint index) public view returns (uint) {
	return dynArray[index];
}

function retrieveFullDynArray() public view returns (uint[] memory) {
	return dynArray;
}

// Find the length of the dynamic array
function getLengthArray() public view returns (uint) {
	return dynArray.length;
}

// Append to array. This will increase the array length by 1.
function appendArray(uint i) public {
	dynArray.push(i);
}

// Remove last element from array. This will decrease the array length by 1
function popArray() public {
	dynArray.pop();
}

// Delete does not change the array length. It resets the value at index to it's default value which in this case is 0
function removeArray(uint index) public {
	delete dynArray[index];
}

/*
retrieveElementArray(0) => uint256 : 10
retrieveElementArray(1) => uint256 : 20
retrieveElementArray(4) => uint256 : 50

retrieveFullDynArray() => uint256[] : [10,20,30,40,50]

getLengthArray() => uint256: 5

appendArray(80) => no return value
dynArray = [10,20,30,40,50,80]

popArray() => no return value
dynArray = [10,20,30,40]

removeArray(2) => no return value
dynArray = [10,20,0,40,50]

*/

```

Dynamic arrays can only be created in storage. However, we can still create *dynamically-allocated* fixed arrays. 

```solidity
// User calling function can determine size of array using the "len" parameter
function createArray(uint len) public pure returns (uint[] memory) {

	// this will still behave like a fixed array so will not inherit dynamic array functions
	uint[] memory arr = new uint[](len);
	return arr;
}
```

### Bytes

`bytes` and `strings` are treated as special arrays that can store an arbitrary length of data. `string` is equal to `bytes` but does not allow length or index access. 

According to the [Solidity docs](https://docs.soliditylang.org/en/v0.8.17/types.html#bytes-and-string-as-arrays):

- As a general rule, use `bytes` for arbitrary-length raw byte data and `string` for arbitrary-length string (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of the value types `bytes1` to `bytes32` because they are much cheaper.
    
    

```solidity
bytes message = "Hello World!";

function retrieveBytes() public view returns (bytes memory) {
	return message;
}

// retrieveBytes() => bytes : 0x48656c6c6f20576f726c6421 ('Hello World!' in ASCII hexadecimal) 
```

We can store bytes temporarily inside functions too:

```solidity
function storeTempBytes() public pure returns (bytes memory) {
	bytes memory message = "Hello World!";
    	return message;
}

// storeTempBytes() => bytes : 0x48656c6c6f20576f726c6421 ('Hello World!' in ASCII hexadecimal) 
```

### Strings

Strings operate in a similar way as with bytes. However unlike bytes, the output would be in ASCII rather than in hexadecimal.

```solidity
string message = "Hello World!";

function retrieveString() public view returns (string memory) {
	return message;
}

// retrieveString() => string : Hello World!
```

We can store strings temporarily inside functions too:

```solidity
function storeTempString() public pure returns (string memory) {
	string memory message = "Hello World!";
    	return message;
}

// storeTempString() => string : Hello World!
```

Both the `bytes` and `string` type have a built-in `concat` function that can be used to concatenate bytes and strings together:

```solidity
// We can continue to use the 'memory' keyword but 'calldata' uses less gas

// Function arguments do not need to be of type 'bytes' and can be value type bytes1..32
function concatBytes(bytes calldata b1, bytes calldata b2) public pure returns (bytes memory) {
	bytes memory b3 = bytes.concat(b1, b2);
    	return b3;
}

/*
Ensure bytes in function arguments are of even length e.g 0xAB or 0xABCD (0xABC would not be permitted)
concatBytes(0xab, 0xcd) => bytes : 0xabcd
*/

function concatString(string calldata s1, string calldata s2) public pure returns (string memory) {
	string memory s3 = string.concat(s1, s2);
    	return s3;
}

/*
concatString("Hello", "World!") => string : Hello World!
*/ 
```

## Mappings

Mappings in Solidity are hash tables that operate similar to a dictionary in other programming languages (e.g. Python)

```solidity
// Intialise the mapping
mapping (address => uint) values;

// msg.sender is a global variable which is set to the address of the function caller
// In this example the user can set a value that will be linked to their address  

function setValue(uint amount) public {
	values[msg.sender] = amount;
}

function checkValue() public view returns (uint) {
	return values[msg.sender];
}

/*
setValue(100) => no return value (but the address has a value of 100 now)

checkValue() => 100 (only if same address was also used for the setValue function above)

*/ 
```

## Structs

[Structs](https://docs.soliditylang.org/en/v0.8.17/types.html#structs) are used to define new types and group together related data and variables.

```solidity
// In this example we will create the struct "Car" and group different variables
struct Car {
	string make;
	string colour;
        uint price;
        bool isPetrol;
}
```

We can also create an array of type `Car` to store our structs:

```solidity

struct Car {
	string make;
    	string colour;
    	uint price;
    	bool isPetrol;
}

// initialise our Car array here
Car[] cars;

// lets create a function to create a Car struct and store it in our Car array
function createCar(string calldata _make, string calldata _colour, uint _price, bool _isPetrol) public { 
	Car memory c  = Car(_make, _colour, _price, _isPetrol);
    	cars.push(c);
}

// we need to return type 'Car' just like other variable types
function retrieveCar(uint index) public view returns (Car memory) {
	return cars[index];
}

/*
createCar("Ford", "Red", 1000, false) => no return value (but new struct has been created)

0 would be the index for the first entry
retrieveCar(0) => tuple(string,string,uint256,bool): Ford,Red,1000,false
*/

```
