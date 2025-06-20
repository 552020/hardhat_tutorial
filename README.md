# Hardhat Tutorial

This is a Hardhat project for Ethereum smart contract development and testing, based on the [official Hardhat tutorial](https://hardhat.org/tutorial).

## Overview

This project implements a basic **custom token** called "My Hardhat Token" (MHT) as a learning example for Ethereum smart contract development. The token demonstrates fundamental concepts of blockchain development including:

### Token Features

- **Name**: "My Hardhat Token"
- **Symbol**: "MHT"
- **Total Supply**: 1,000,000 tokens (fixed supply)
- **Divisibility**: Non-divisible tokens (can transfer 1, 2, 3, or 37 tokens but not 2.5)
- **Owner**: The account that deploys the contract receives the entire initial supply

### Smart Contract Functionality

- **Token Transfer**: Users can transfer tokens to other addresses
- **Balance Checking**: Anyone can check token balances of any address
- **Event Emission**: Transfer events are emitted for off-chain tracking
- **Access Control**: Basic ownership tracking for the deploying account

### Important Note

This token is **NOT ERC-20 compatible**. While it implements basic token functionality, it doesn't follow the ERC-20 standard. This is intentional for simplicity in learning. Real-world tokens like DAI and USDC implement the full ERC-20 standard for compatibility with wallets, exchanges, and other DeFi applications.

### Key Learning Objectives

This contract serves as an introduction to:

- **Solidity basics**: Variables, functions, mappings, events
- **Smart contract deployment**: Using Hardhat for development
- **Testing**: Comprehensive test coverage with fixtures and assertions
- **Token economics**: Fixed supply token distribution
- **Security**: Basic validation and error handling

The contract is intentionally simple to focus on core concepts while providing a foundation for more complex token implementations.

## Project Structure

```
hardhat-tutorial/
├── contracts/
│   └── Token.sol          # ERC20 token contract
├── test/
│   └── Token.js           # Comprehensive test suite for Token contract
├── artifacts/             # Compiled contracts
├── cache/                 # Hardhat cache
├── hardhat.config.js      # Hardhat configuration
├── package.json           # Project dependencies
└── README.md             # This file
```

## Setup and Installation

1. **Install dependencies:**

   ```bash
   npm install
   ```

2. **Install required packages:**
   ```bash
   npm install --save-dev @nomicfoundation/hardhat-toolbox
   npm install --save-dev chai@4.3.7
   ```

## Configuration

### Hardhat Config (`hardhat.config.js`)

The configuration includes the Hardhat Toolbox plugin which provides essential tools for development:

```javascript
/** @type import('hardhat/config').HardhatUserConfig */
require("@nomicfoundation/hardhat-toolbox");
module.exports = {
  solidity: "0.8.28",
};
```

### Test Configuration (`test/Token.js`)

The test suite includes comprehensive testing with fixtures, event testing, and error handling:

```javascript
const { expect } = require("chai");
const { loadFixture } = require("@nomicfoundation/hardhat-toolbox/network-helpers");

describe("Token contract", function () {
  async function deployTokenFixture() {
    const [owner, addr1, addr2] = await ethers.getSigners();
    const hardhatToken = await ethers.deployContract("Token");
    await hardhatToken.waitForDeployment();
    return { hardhatToken, owner, addr1, addr2 };
  }

  describe("Deployment", function () {
    it("Should set the right owner", async function () {
      const { hardhatToken, owner } = await loadFixture(deployTokenFixture);
      expect(await hardhatToken.owner()).to.equal(owner.address);
    });

    it("Should assign the total supply of tokens to the owner", async function () {
      const { hardhatToken, owner } = await loadFixture(deployTokenFixture);
      const ownerBalance = await hardhatToken.balanceOf(owner.address);
      expect(await hardhatToken.totalSupply()).to.equal(ownerBalance);
    });
  });

  describe("Transactions", function () {
    it("Should transfer tokens between accounts", async function () {
      const { hardhatToken, owner, addr1, addr2 } = await loadFixture(deployTokenFixture);
      await expect(hardhatToken.transfer(addr1.address, 50)).to.changeTokenBalances(
        hardhatToken,
        [owner, addr1],
        [-50, 50]
      );
    });

    it("Should emit Transfer events", async function () {
      const { hardhatToken, owner, addr1 } = await loadFixture(deployTokenFixture);
      await expect(hardhatToken.transfer(addr1.address, 50))
        .to.emit(hardhatToken, "Transfer")
        .withArgs(owner.address, addr1.address, 50);
    });

    it("Should fail if sender doesn't have enough tokens", async function () {
      const { hardhatToken, owner, addr1 } = await loadFixture(deployTokenFixture);
      await expect(hardhatToken.connect(addr1).transfer(owner.address, 1)).to.be.revertedWith("Not enough tokens");
    });
  });
});
```

## Testing Features

### Test Organization

- **Nested `describe` blocks** for organizing tests into logical groups
- **Fixtures** using `loadFixture` for efficient test setup and teardown
- **Comprehensive coverage** including deployment, transactions, events, and error cases

### Advanced Assertions

- **Token balance changes** using `.changeTokenBalances()` matcher
- **Event emission** testing with `.emit()` and `.withArgs()`
- **Transaction reverts** testing with `.revertedWith()`

### Performance Optimization

- **Fixtures** run setup once and snapshot the state for subsequent tests
- **Automatic compilation** when contracts change
- **Fast test execution** with Hardhat Network

## Issues and Solutions

### Problem 1: Missing `chai` dependency

**Error:** `Cannot find module 'chai'`

**Solution:** Install chai as a dev dependency:

```bash
npm install --save-dev chai@4.3.7
```

**Note:** We use version 4.3.7 specifically because newer versions of chai are ES modules only and don't support CommonJS `require()` statements.

### Problem 2: ES Module compatibility issues

**Error:** `require() of ES Module chai.js not supported`

**Root Cause:** Newer versions of chai (v5+) are ES modules only.

**Solution:** Downgrade to chai v4.3.7 which supports CommonJS.

### Problem 3: `ethers` not defined in tests

**Error:** `ReferenceError: ethers is not defined`

**Solution:**

1. Install Hardhat Toolbox: `npm install --save-dev @nomicfoundation/hardhat-toolbox`
2. Add the toolbox to hardhat.config.js: `require("@nomicfoundation/hardhat-toolbox")`
3. Explicitly require ethers in test files: `const { ethers } = require("hardhat")`

## Running Tests

```bash
npx hardhat test
```

Expected output:

```
  Token contract
    Deployment
      ✔ Should set the right owner (336ms)
      ✔ Should assign the total supply of tokens to the owner
    Transactions
      ✔ Should transfer tokens between accounts
      ✔ Should emit Transfer events
      ✔ Should fail if sender doesn't have enough tokens

  5 passing (363ms)
```

## Available Scripts

- `npx hardhat test` - Run all tests
- `npx hardhat compile` - Compile contracts
- `npx hardhat node` - Start local blockchain
- `npx hardhat run scripts/deploy.js --network localhost` - Deploy contracts

## Dependencies

### Core Dependencies

- `hardhat` - Ethereum development environment
- `@nomicfoundation/hardhat-toolbox` - Collection of Hardhat plugins and tools

### Testing Dependencies

- `chai@4.3.7` - Assertion library (CommonJS compatible version)
- `mocha` - Test framework (included with hardhat-toolbox)
- `ethers` - Ethereum library (included with hardhat-toolbox)
- `@nomicfoundation/hardhat-chai-matchers` - Advanced chai matchers for smart contracts (included with hardhat-toolbox)

## Key Learnings

1. **Module System Compatibility:** When using CommonJS (`require`), ensure all dependencies support CommonJS syntax.

2. **Hardhat Toolbox:** The `@nomicfoundation/hardhat-toolbox` package provides essential plugins including:

   - `hardhat-ethers` for ethers.js integration
   - `hardhat-chai-matchers` for additional chai matchers
   - `hardhat-gas-reporter` for gas usage reporting
   - `hardhat-coverage` for test coverage

3. **Test Fixtures:** Use `loadFixture` for efficient test setup that runs once and snapshots the state for subsequent tests.

4. **Advanced Testing:** Leverage Hardhat's chai matchers for testing token transfers, events, and transaction reverts.

5. **Explicit Imports:** In test files, explicitly import `ethers` from hardhat rather than relying on global variables.

6. **Version Pinning:** Sometimes it's necessary to pin specific versions of dependencies to maintain compatibility.

## Testing Best Practices

1. **Use Fixtures:** Avoid code duplication and improve performance with `loadFixture`
2. **Test Events:** Verify that your contracts emit the correct events with proper arguments
3. **Test Failures:** Ensure your contracts properly revert when they should
4. **Organize Tests:** Use nested `describe` blocks to group related tests
5. **Comprehensive Coverage:** Test both happy path and edge cases

## Troubleshooting

If you encounter similar issues:

1. Check if all dependencies support your module system (CommonJS vs ES Modules)
2. Ensure Hardhat Toolbox is installed and configured
3. Verify that ethers is explicitly imported in test files
4. Consider using the exact versions specified in this README
5. Make sure your contract has the required functions (like `owner()`) for the tests

## Next Steps

- Add more comprehensive tests
- Implement additional smart contracts
- Set up deployment scripts
- Configure networks for different environments
- Add gas optimization testing
- Implement integration tests
# hardhat_tutorial
