# Hardhat Network

## Overview

**Hardhat comes with its own built-in local Ethereum network**, often referred to as the **Hardhat Network**. It's a powerful tool for local development and testing of smart contracts.

---

## ✅ What Is the Hardhat Network?

It's a **local in-memory Ethereum network** that runs when you:

- execute a script (`npx hardhat run scripts/deploy.js`)
- or explicitly launch it (`npx hardhat node`)

It's **instant**, fast, and resets on every run unless you keep it alive manually.

### Key Features

- **Local**: Runs entirely on your machine
- **Fast**: Instant transaction processing
- **Free**: No real gas costs
- **Isolated**: Perfect for development and testing
- **Preloaded**: Comes with test accounts and ETH

---

## 🔍 Two Ways to Use It

### 1. **Ephemeral Mode (default)**

```bash
npx hardhat run scripts/deploy.js
```

- Spins up a local network
- Runs the script
- Then **shuts down**
- Good for quick testing

**Use case**: When you want to quickly test a deployment or script without maintaining a persistent network.

### 2. **Persistent Mode**

```bash
npx hardhat node
```

- Starts a **persistent** local blockchain at `http://localhost:8545`
- Comes preloaded with 20 accounts (each with 10000 ETH)
- Keeps chain state (so you can test UIs or interact multiple times)

Then deploy to it via:

```bash
npx hardhat run scripts/deploy.js --network localhost
```

**Use case**: When you want to interact with your contracts multiple times, test UIs, or simulate a real blockchain environment.

---

## 🔐 Wallets for Local Testing

You can import the preloaded accounts into MetaMask using the **private keys** printed by `npx hardhat node`. That way, you can test transactions in your browser **without needing real ETH or using a public testnet**.

### Setting up MetaMask for Local Development

1. **Start Hardhat Network**:

   ```bash
   npx hardhat node
   ```

2. **Copy Private Keys**: The command will output private keys for 20 accounts

3. **Add Network to MetaMask**:

   - Network Name: `Hardhat Local`
   - RPC URL: `http://localhost:8545`
   - Chain ID: `31337`
   - Currency Symbol: `ETH`

4. **Import Account**: Use one of the private keys to import an account

### Example Account Setup

When you run `npx hardhat node`, you'll see output like:

```
Account #0: 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

---

## 🧪 Testnet vs Hardhat Network

| Feature           | Hardhat Network | Public Testnet (e.g. Sepolia) |
| ----------------- | --------------- | ----------------------------- |
| Runs locally      | ✅              | ❌                            |
| Needs internet    | ❌              | ✅                            |
| Preloaded ETH     | ✅ (10000 ETH)  | ❌ (need faucet)              |
| Realistic latency | ❌ (instant)    | ✅                            |
| Public visibility | ❌              | ✅                            |
| Gas costs         | ❌ (free)       | ✅ (real gas costs)           |
| Network effects   | ❌              | ✅                            |

---

## 🚀 Getting Started

### Quick Start

1. **Deploy to ephemeral network**:

   ```bash
   npx hardhat run scripts/deploy.js
   ```

2. **Start persistent network**:

   ```bash
   npx hardhat node
   ```

3. **Deploy to persistent network**:
   ```bash
   npx hardhat run scripts/deploy.js --network localhost
   ```

### Configuration

The Hardhat Network is configured in your `hardhat.config.js`:

```javascript
module.exports = {
  solidity: "0.8.28",
  networks: {
    hardhat: {
      // Hardhat Network configuration
    },
    localhost: {
      url: "http://localhost:8545",
    },
  },
};
```

---

## 🛠️ Advanced Features

### Mining Configuration

You can configure mining behavior:

```javascript
module.exports = {
  solidity: "0.8.28",
  networks: {
    hardhat: {
      mining: {
        auto: true, // Auto mine blocks
        interval: 0, // Mine immediately
      },
    },
  },
};
```

### Forking Mainnet

You can fork the mainnet for more realistic testing:

```javascript
module.exports = {
  solidity: "0.8.28",
  networks: {
    hardhat: {
      forking: {
        url: `https://eth-mainnet.alchemyapi.io/v2/${ALCHEMY_API_KEY}`,
        blockNumber: 14390000,
      },
    },
  },
};
```

---

## ✅ Summary

Hardhat Network provides its **own test network**, which is ideal for:

- **Local development** - Fast iteration cycles
- **Automated testing** - Reliable test environment
- **Fast deployments** - No gas costs or delays
- **UI testing** - Persistent state for frontend development

### When to Use Each

- **Hardhat Network**: Development, testing, debugging
- **Public Testnet**: Integration testing, user acceptance testing
- **Mainnet**: Production deployment

For simulating the real network experience, use **Sepolia** or another public testnet alongside Hardhat Network.

---

## 📚 Related Documentation

- [Hardhat Network Official Docs](https://hardhat.org/hardhat-network)
- [Deployment Guide](./deployment.md)
- [Testing Guide](./testing.md)
- [Configuration Guide](./configuration.md)
