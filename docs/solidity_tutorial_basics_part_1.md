# Solidity Tutorial - Basics (Part 1)

[Solidity](https://docs.soliditylang.org/en/v0.8.19/#solidity) is an object-oriented, high-level language for implementing smart contracts. It is influenced by other high level languages such as C++, Python and JavaScript for writing smart contracts specifically for the EVM (Ethereum Virtual Machine). Whilst smart contracts can be written in various different languages, Solidity continues to remain the [most popular amongst blockchain developers](https://blog.chain.link/smart-contract-programming-languages/) (as of writing of course).  

In these series of tutorials, we will be covering certain key concepts, so that by the end, you will be able to create your very own smart contract 🙂

## **SPDX License Identifier**

On the top of some smart contracts, you may find something like this: 

```solidity
// SPDX-License-Identifier: MIT
```

This line is simply a machine-readable [SPDX license identifier](https://spdx.org/) that indicates which license you would like your source code to fall under. In the example above, the smart contract will use the [MIT](https://opensource.org/license/mit/)  license. Having a license identifier is not mandatory and you will be able to compile your smart contracts just fine without it. It will be important if you do wish to open-source your code so that others can modify and redistribute your code. 

If you don’t wish to use the MIT license, the full SPDX license list can be found [here](https://spdx.org/licenses/).

## Pragmas

After including your license, the next thing you may come across is something like this:

```solidity
pragma solidity >=0.8.2 <0.9.0;
```

[Pragmas](https://docs.soliditylang.org/en/v0.8.19/layout-of-source-files.html#pragmas) are used in Solidity to enable certain compiler features and checks. One of the most common is the [version pragma](https://docs.soliditylang.org/en/v0.8.19/layout-of-source-files.html#version-pragma) (as shown above) which checks that the version of the Solidity compiler matches the version explicitly stated in the smart contract. You can specify a single, fixed version number or a specific range using comparison operators.

Version pragma is important as each `0.x.0` introduces breaking changes that may make your smart contract incompatible with the previous version. The version pragma is not mandatory but it will certainly help eliminate a lot of compilation errors and bugs if you knew which version the smart contract was compiled under!   

## Imports

Like with any other code you would write, you may need to import other files/libraries into your current code. With Solidity, you can [import both local and external files](https://solidity-by-example.org/import/) using the `import` keyword.

### Local

 If you have two files *Foo.sol* and *Import.sol* in a file structure like this:

```solidity
├── Import.sol
└── Foo.sol
```

You can import Foo.sol into Import.sol by specifying its relative path:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

// import Foo.sol from current directory
import "./Foo.sol";
```

### External

You can also [import from external sources](https://remix-ide.readthedocs.io/en/latest/import.html) such as from GitHub, NPM and IPFS:

```solidity
// Import from GithHub URL
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v2.5.0/contracts/math/SafeMath.sol";

// Import from NPM
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

// Import from IPFS
import 'ipfs://Qmdyq9ZmWcaryd1mgGZ4PttRNctLGUSAMpPqufsk6uRMKh';
```

## Comments

Solidity allows for [single and multi-line comments](https://docs.soliditylang.org/en/v0.8.19/layout-of-source-files.html#comments). Single line comments are denoted with `//` and multi-line comments are denoted with `/*...*/`

```solidity
// This is a single-line comment.

/*
This is a
multi-line comment.
*/
```