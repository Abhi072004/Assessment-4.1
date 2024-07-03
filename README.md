# DeFi Empire - A Simple DeFi Kingdom Clone
DeFi Empire is a thrilling blockchain-based gaming experience where players can collect, build, and battle with their digital assets on the Avalanche network. This project aims to create a DeFi Kingdom clone, leveraging the power of Avalanche's EVM Subnet for low fees and custom token capabilities. By following this guide, you'll learn how to set up your EVM subnet, deploy essential smart contracts, and take the first steps toward building your very own DeFi empire.
## Description
DeFi Empire is a decentralized finance (DeFi) game inspired by DeFi Kingdoms, designed to operate on the Avalanche network. The game allows players to engage in various activities such as battling, exploring, and trading, all rewarded with in-game tokens. These tokens can be used to purchase assets or traded with other players. The project focuses on setting up a custom EVM subnet and deploying two fundamental smart contracts: an ERC20 token contract and a Vault contract. These components serve as the building blocks for the game's economy and reward system.
### Getting Started
**1. Setup your EVM subnet**

Firstly to setup EVM subnet in your machine you require linux operating system. If you have then go to the official documentation **https://docs.avax.network/build/subnet/hello-subnet** to create a custom EVM subnet on the Avalanche network.

**2. Define Your Native Currency**

Create your own native currency to serve as the in-game currency for your DeFi Kingdom clone.

**3. Connect to MetaMask**

Add your EVM Subnet to MetaMask by following the steps:
1) Go to add network option in metamask wallet.
2) Click on add a network manually.
3) Enter network name, the rpc url, chain Id, and symbol provided by avalanche CLI when you deployed your subnet.
4) Click on save.
5) Ensure it is the selected network in MetaMask.

**4. Deploy smart contracts**

Use Solidity and Remix to deploy the basic building blocks of your game, such as smart contracts for battling, exploring, and trading. For example here we have two smart contracts:
1) *ERC20* - Deploy the ERC20 token contract to establish the in-game currency.
   
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "Mirror";
    string public symbol = "MRR";
    uint8 public decimals = 18;

	  event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```
2) *Vault* - Deploy the Vault contract to manage deposits and withdrawals.
``` Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC20 {
    function totalSupply() external view returns (uint);

    function balanceOf(address account) external view returns (uint);

    function transfer(address recipient, uint amount) external returns (bool);

    function allowance(address owner, address spender) external view returns (uint);

    function approve(address spender, uint amount) external returns (bool);

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}

contract Vault {
    IERC20 public immutable token;

    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }

    function _burn(address _from, uint _shares) private {
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }

    function deposit(uint _amount) external {
        uint shares;
        if (totalSupply == 0) {
            shares = _amount;
        } else {
            shares = (_amount * totalSupply) / token.balanceOf(address(this));
        }

        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint _shares) external {
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        token.transfer(msg.sender, amount);
    }

    /*
    a = amount
    B = balance of token before withdraw
    T = total supply
    s = shares to burn

    (T - s) / T = (B - a) / B 

    a = sB / T
    */
}
```

**5. Test your application**

Using Remix, interact with your deployed smart contracts to test their functionality. Deploy tokens, pools, and other game elements to ensure everything works as expected.

## Authors
Abhishek

## License
This project is licensed under the MIT license.

