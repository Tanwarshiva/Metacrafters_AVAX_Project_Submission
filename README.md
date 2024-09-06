# DeFi Kingdom Clone on Avalanche Metacrafters Project

![Avalanche Logo](https://cdn.prod.website-files.com/632993e1d1acbfa5635afd0b/6351544c2e41652a8bf6a2af_Logo%20Full%20Red.svg)

Welcome to the DeFi Kingdom Clone project! This project guides you through creating a decentralized finance (DeFi) game inspired by DeFi Kingdoms, built on the Avalanche network. In this game, players can collect, build, and battle using digital assets, earning rewards through various activities.

## Key Features

- **Custom EVM Subnet:** Deploy your own smart contracts on a custom chain within Avalanche, with low fees and a custom token.
- **Game Mechanics:** Implement game activities like battling, exploring, and trading, with rewards distributed as tokens.
- **Metamask Integration:** Connect your EVM Subnet to Metamask for seamless asset management and interaction.
- **Additional Enhancements:** Add features such as leaderboards, achievements, and varying difficulty levels to create an immersive gaming experience.

## Getting Started

### Prerequisites

Ensure you have the following installed:

- [Avalanche CLI](https://docs.avax.network/)
- [Node.js](https://nodejs.org/)
- [Metamask](https://metamask.io/)
- [Remix IDE](https://remix.ethereum.org/) or another Solidity development environment

### Setup and Installation

1. **Clone the Repository**

   ```bash
   git clone https://github.com/yourusername/defi-kingdom-clone.git
   cd defi-kingdom-clone
   ```

2. **Set Up Your EVM Subnet**

   Follow the [Avalanche Subnet documentation](https://docs.avax.network/) to create a custom EVM Subnet.

3. **Define Your Native Currency**

   Customize your own native currency within the Subnet that will act as the in-game currency for your clone.

4. **Connect to Metamask**

   Connect your custom EVM Subnet to Metamask using the network settings provided by Avalanche.

5. **Deploy Contracts Using Remix**

   Open Remix IDE and deploy the provided smart contracts:

   - **ERC20 Token Contract:** Basic token functionality for your game's currency.
   - **Vault Contract:** Manages deposits and withdrawals of your in-game tokens.

### Example Contracts and Explanations

#### ERC20 Token Contract

**Purpose:**  
The ERC20 Token Contract is a standard token contract that defines the in-game currency. It allows minting new tokens, transferring tokens between players, and burning tokens when necessary. This token can be used in various game activities like purchasing items, rewards, or trading with other players.

**Key Functions:**
- `transfer`: Allows a user to send tokens to another address.
- `approve`: Allows a user to approve another address to spend tokens on their behalf.
- `transferFrom`: Allows an approved address to transfer tokens on behalf of the owner.
- `mint`: Allows minting of new tokens, increasing the total supply.
- `burn`: Allows burning of tokens, reducing the total supply.

**Contract Code:**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "GameToken";
    string public symbol = "GT";
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

    function transferFrom(address sender, address recipient, uint amount) external returns (bool) {
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

#### Vault Contract

**Purpose:**  
The Vault Contract is a decentralized vault that allows players to deposit and withdraw tokens. It manages the in-game economy by providing a secure place for players to store their tokens, and also serves as a mechanism for staking and earning rewards based on the amount deposited.

**Key Functions:**
- `deposit`: Allows players to deposit a specified amount of tokens into the vault, receiving shares in return which represent their stake in the vault.
- `withdraw`: Allows players to withdraw their deposited tokens based on the number of shares they own, which are burned upon withdrawal.
- `_mint`: A private function to mint shares when tokens are deposited.
- `_burn`: A private function to burn shares when tokens are withdrawn.

**Contract Code:**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC20 {
    function totalSupply() external view returns (uint);
    function balanceOf(address account) external view returns (uint);
    function transfer(address recipient, uint amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint);
    function approve(address spender, uint amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint amount) external returns (bool);

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
}
```

## Next Steps

After deploying these foundational contracts, you can expand your game by:

- Adding new game mechanics (e.g., battles, quests).
- Implementing leaderboards and achievements.
- Developing a front-end interface to interact with the blockchain.
