The process of bridging ETH to ckETH

This repository provides a step-by-step guide and code to bridge Ethereum (ETH) to ckETH, a custom wrapped version of ETH on ICP blockchain.


Bridging allows users to convert native ETH on the Ethereum network to ckETH, which is a wrapped representation of ETH on another chain or layer. This process involves interacting with smart contracts on both networks and managing the transfer of assets securely.

THE CHARACTERISTICS 
- Secure bridging between Ethereum and ckETH.
- Instructions for deploying necessary smart contracts.
- Integration with popular Ethereum wallets (e.g., MetaMask, Plug).
  
 Prerequisites
- Ethereum wallet (MetaMask, Trust Wallet, etc.)
- Access to the Ethereum network (Mainnet or Testnet)
- Basic understanding of Solidity and blockchain development

Here is the step by step process to Bridge ETH to ckETH

1. Install dependencies ( Hardhat, Ethers.js)
2. Deploy the bridging contracts (instructions below)
3. Interact with the bridge using a web interface or script
4. Convert ETH to ckETH

  Smart Contracts

You can find the smart contract code for the bridge in the `contracts/` directory.

  Deploying the Smart Contracts

1. Set up your local environment with Hardhat or Truffle.
2. Compile and deploy the smart contracts using the following commands:
   ```bash
   npx hardhat compile
   npx hardhat run scripts/deploy.js --network <network_name>

   You can use the script in scripts/bridge.js to bridge your ETH to ckETH follow this step adherely 
   
node scripts/bridge.js --amount <amount> --address <your_wallet_address>

   Create the Smart Contract
You’ll need Solidity smart contracts that handle the conversion between ETH and `ckETH`. This could involve creating a wrapped token contract for `ckETH` and a bridge contract.

- ERC-20 Contract for `ckETH`: You can use a standard ERC-20 contract with some modifications.
- **Bridge Contract**: This contract will handle the locking of ETH on Ethereum and the minting/releasing of `ckETH`.
- 


 Then this below code is for solidity
 
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract ckETH is ERC20 {
    constructor() ERC20("Custom Wrapped ETH", "ckETH") {
        _mint(msg.sender, 1000000 * 10 ** decimals());
    }
}

or Use this basic method on solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./ckETH.sol";

contract ETHtoCKETHBridge {
    ckETH public ckethToken;

    constructor(ckETH _ckethToken) {
        ckethToken = _ckethToken;
    }

    // Lock ETH and mint ckETH
    function lockAndMint(uint256 _amount) public payable {
        require(msg.value == _amount, "Incorrect ETH amount sent");
        // Mint ckETH to sender
        ckethToken.mint(msg.sender, _amount);
    }

    // Burn ckETH and unlock ETH
    function burnAndUnlock(uint256 _amount) public {
        require(ckethToken.balanceOf(msg.sender) >= _amount, "Not enough ckETH");
        ckethToken.burn(msg.sender, _amount);
        payable(msg.sender).transfer(_amount);
    }
}

For js.

async function main() {
    const [deployer] = await ethers.getSigners();

    console.log("Deploying contracts with the account:", deployer.address);

    const ckETH = await ethers.getContractFactory("ckETH");
    const ckethToken = await ckETH.deploy();
    console.log("ckETH token deployed to:", ckethToken.address);

    const Bridge = await ethers.getContractFactory("ETHtoCKETHBridge");
    const bridge = await Bridge.deploy(ckethToken.address);
    console.log("Bridge contract deployed to:", bridge.address);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
