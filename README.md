# Token_Vesting_Contract
A token vesting smart contract.

Step 1: Go to https://wizard.openzeppelin.com/#

Premint 10,000 tokens or any token amount of your choice:

![Screenshot 2022-10-11 at 13 00 19](https://user-images.githubusercontent.com/29931071/195085933-798556be-f095-4c3e-856c-b0261b9f805b.png)

Then click on "open in remix" to open up and write the rest of the code in Remix IDE

My Remix IDE looks like this:

![Screenshot 2022-10-11 at 13 07 33](https://user-images.githubusercontent.com/29931071/195087261-73731aeb-3665-4954-b103-f5393e113dfd.png)

Now go ahead to create a new file called vesting.sol

In vesting.sol, copy the license line and the pragma solidity line and define a new Vesting Contract

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Vesting {
    
}
```

Inside the Vesting contract, write a constructor which is the first thing that will be called in a smart contract.
The constructor will take as its argument 1. The address of the token.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Vesting {

    constructor (address _token) {

    }
}
```
