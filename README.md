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

Now we will define our token as an IERC20 token and give it a token variable, after which we will write a variable on top on the constructor:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Vesting {

    IERC20 token;
    
    constructor (address _token) {
        token = IERC20(_token)
    }
}
```

Now if you check, the auto-compiler is showing an error:

![Screenshot 2022-10-11 at 13 17 10](https://user-images.githubusercontent.com/29931071/195089506-9e214999-181e-4a9d-963c-3bdc9ef29a16.png)

This is because we've not imported OpenZeppelin ERC20 class from the previous solidity contract:

We're good now.

Also notice that the URl has IERC20.sol instead of ERC20:

![Screenshot 2022-10-11 at 13 20 56](https://user-images.githubusercontent.com/29931071/195090849-dde8568c-2ae5-4988-ae28-2ed4099b5934.png)

Now we will create a new function called Lock:

This function will take in 4 arguments: _from, _receiver, _amount and _expiry. We will also make it an external function.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        

    }
}
```

First we will define a token transfer

![Screenshot 2022-10-11 at 13 31 14](https://user-images.githubusercontent.com/29931071/195093157-6d0db7e0-3c98-4185-96db-938240844c45.png)

 Then we will define a reciever, and also i will define my variables on top of the constructor:
 
 ```
 // SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;
    address receiver;
    uint amount;
    uint expiry;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        token.transferFrom(_from, address(this), amount);
        receiver = _receiver;
        amount = _amount;
        expiry = _expiry;
    }
}
```

Now just so that no one can called this function twice, i will create a new 'locked' variable which will start as false, and make all our variables public:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;
    address public receiver;
    uint public amount;
    uint public expiry;
    bool public locked = false;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        token.transferFrom(_from, address(this), amount);
        receiver = _receiver;
        amount = _amount;
        expiry = _expiry;
        locked = true;
    }
}
```

Also i will use a require statement to require that this lock has not happened before:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;
    address public receiver;
    uint public amount;
    uint public expiry;
    bool public locked = false;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        require(!locked, "We have already locked tokens");
        token.transferFrom(_from, address(this), amount);
        receiver = _receiver;
        amount = _amount;
        expiry = _expiry;
        locked = true;
    }
}
```

Now that we already have our lock function, now we will write a withdraw function which will transfer the tokens to the receiver with a specified amount:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;
    address public receiver;
    uint public amount;
    uint public expiry;
    bool public locked = false;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        require(!locked, "We have already locked tokens");
        token.transferFrom(_from, address(this), amount);
        receiver = _receiver;
        amount = _amount;
        expiry = _expiry;
        locked = true;
    }

    function withdraw() external {
        token.transfer(receiver, amount);
    }
}
```

We will now create a new variable called 'claimed' which will be set to false, and we will make sure that this claim has not happened with a require statement.

And before we transfer, we will set claimed to be true, if not we will suffer multiple claims.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;
    address public receiver;
    uint public amount;
    uint public expiry;
    bool public locked = false;
    bool public claimed = false;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        require(!locked, "We have already locked tokens");
        token.transferFrom(_from, address(this), amount);
        receiver = _receiver;
        amount = _amount;
        expiry = _expiry;
        locked = true;
    }

    function withdraw() external {
        require(!claimed, "Tokens have already been claimed");
        claimed = true;
        token.transfer(receiver, amount);
    }
}
```

Now we will set the expiry with another require statement:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;
    address public receiver;
    uint public amount;
    uint public expiry;
    bool public locked = false;
    bool public claimed = false;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        require(!locked, "We have already locked tokens");
        token.transferFrom(_from, address(this), amount);
        receiver = _receiver;
        amount = _amount;
        expiry = _expiry;
        locked = true;
    }

    function withdraw() external {
        require(block.timestamp > expiry, "Tokens have not be unlocked");  
        require(!claimed, "Tokens have already been claimed");
        claimed = true;
        token.transfer(receiver, amount);
    }
}
```

Now we will write a getTime function:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts@4.7.3/token/ERC20/IERC20.sol";

contract Vesting {

    IERC20 token;
    address public receiver;
    uint public amount;
    uint public expiry;
    bool public locked = false;
    bool public claimed = false;

    constructor (address _token) {
        token = IERC20(_token);
    }

    function lock(address _from, address _receiver, uint256 _amount, uint256 _expiry) external {
        require(!locked, "We have already locked tokens");
        token.transferFrom(_from, address(this), amount);
        receiver = _receiver;
        amount = _amount;
        expiry = _expiry;
        locked = true;
    }

    function withdraw() external {
        require(block.timestamp > expiry, "Tokens have not be unlocked");  
        require(!claimed, "Tokens have already been claimed");
        claimed = true;
        token.transfer(receiver, amount);
    }

    function getTime() external view returns (uint256) {
        return block.timestamp;
    }
}
```


Now go ahead and compile, then deploy the smart contract.

Deploy the first smart contract we imported from open zeppelin first:

![Screenshot 2022-10-11 at 13 52 07](https://user-images.githubusercontent.com/29931071/195097988-179957a7-5f8e-4cf5-8af4-234cd1db77ff.png)


Now copy the token address 

Then paste it inside the deploy box for Vesting.sol:

![Screenshot 2022-10-11 at 13 54 11](https://user-images.githubusercontent.com/29931071/195098436-3c20cc00-3907-4d1e-baf8-e0741bd271d6.png)


Then scroll down to see that the vesting token has been deployed:

![Screenshot 2022-10-11 at 13 57 06](https://user-images.githubusercontent.com/29931071/195098875-5da76a30-cc88-4944-aabb-2cf6975d76d0.png)


You will see how we're able to lock tokens.

![Screenshot 2022-10-11 at 13 59 18](https://user-images.githubusercontent.com/29931071/195099407-3dbbdb7e-3508-4ec8-b9d7-a7f4923ea282.png)


   




