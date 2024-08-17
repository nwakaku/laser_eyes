# Laser Eyes Integration Guide
# By Wisdom Chris

**Submission to Buidlbox Bounty Hackathon [OP_HACK001 🟠 hackathon] Track: "Laser Eyes Integration Guide Challenge" and "PSBT Educational Content Challenge"**


## Objective

Laser Eyes is a powerful tool designed to simplify wallet connections for Bitcoin applications. This guide will walk you through integrating Laser Eyes into your project, offering step-by-step instructions, code samples, and best practices. Whether you're developing a straightforward Bitcoin app or a complex decentralized application (dApp), this guide will help you leverage Laser Eyes to enhance your developer experience and streamline wallet interactions.


## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Key Features](#key-features)
- [Common Use Cases](#common-use-cases)
  - [Connecting Wallets](#connecting-wallets)
  - [Sending Bitcoin](#sending-bitcoin)
  - [Signing Messages](#signing-messages)
  - [Handling PSBTs](#handling-psbts)
  - [Switching Networks](#switching-networks)
  - [Error Handling](#error-handling)
- [Advanced Usage](#advanced-usage)
  - [Custom Configuration](#custom-configuration)
  - [Best Practices](#best-practices)
- [Potential Issues](#potential-issues)
- [API Reference](#api-reference)

---

## Installation

Laser Eyes can be installed using popular package managers. Choose the one that fits your development environment:

### Using Yarn

```bash
yarn add @omnisat/lasereyes
```

### Using npm

```bash
npm install @omnisat/lasereyes
```

### Using pnpm

```bash
pnpm add @omnisat/lasereyes
```

### Using Bun

```bash
bun add @omnisat/lasereyes
```

## Quick Start

1. **Wrap your app with `LaserEyesProvider`:**

   To initialize Laser Eyes in your application, wrap your components with `LaserEyesProvider`. Make sure to configure the correct network (mainnet or testnet).

   ```javascript
   import { LaserEyesProvider, TESTNET } from "@omnisat/lasereyes";
   
   function App() {
     return (
       <LaserEyesProvider config={{ network: TESTNET }}>
         {/* Your app components */}
         <WalletConnector />
       </LaserEyesProvider>
     );
   }
   ```

   **Note:** Do not render `LaserEyesProvider` during Server-Side Rendering (SSR). Ensure it is conditionally rendered on the client-side to prevent conflicts.

2. **Use the `useLaserEyes` hook to interact with wallets:**

   The `useLaserEyes` hook gives you access to wallet functions such as connecting to a wallet, sending Bitcoin, and signing messages.

   ```javascript
   import { useLaserEyes, UNISAT } from "@omnisat/lasereyes";

   function WalletConnector() {
     const {
       connect,
       connected,
       address,
       balance,
       sign,
       sendBTC,
       switchNetwork,
     } = useLaserEyes();

     const handleConnect = () => {
       connect(UNISAT);
     };

     return (
       <div>
         {!connected ? (
           <button onClick={handleConnect}>Connect Wallet</button>
         ) : (
           <div>
             <p>Connected Address: {address}</p>
             <p>Balance: {balance.total}</p>
           </div>
         )}
       </div>
     );
   }
   ```

## Key Features

- **Multi-Wallet Support:** Seamless integration with Bitcoin wallets like OYL, Unisat, Xverse, and Leather.
- **React Integration:** Context setup with `LaserEyesProvider` and wallet functions with `useLaserEyes`.
- **Comprehensive Wallet Operations:** Balance checking, transaction signing, and message signing are fully supported.
- **TypeScript Support:** Strong typing for improved productivity and code reliability.
- **Network Switching:** Easily switch between mainnet and testnet.
- **Error Handling:** Consistent error handling across different wallet implementations.

## Common Use Cases

### Connecting Wallets

Use the `connect()` method to connect to a wallet. Make sure to check for wallet availability before attempting a connection.

```javascript
const { connect, connected, hasUnisat } = useLaserEyes();

if (hasUnisat && !connected) {
  await connect(UNISAT);
}
```

### Sending Bitcoin

Send Bitcoin to a specific address using the `sendBTC()` method. Ensure you have sufficient funds in the wallet.

```javascript
const recipient = "bc1qar0srrr7xfkvy5l643lydnw9re59gtzzwf5mdq";
const amount = 1000; // in satoshis

try {
  const txId = await sendBTC(recipient, amount);
  console.log("Transaction ID:", txId);
} catch (error) {
  console.error("Error sending BTC:", error);
}
```

### Signing Messages

Sign messages securely using the `sign()` method. This can be useful for proving ownership of a wallet address without revealing the private key.

```javascript
const message = "Hello, Bitcoin!";

try {
  const signature = await sign(message);
  console.log("Message signed. Signature:", signature);
} catch (error) {
  console.error("Error signing message:", error);
}
```

### Handling PSBTs

You can sign and push Partially Signed Bitcoin Transactions (PSBTs) using the `signPsbt()` and `pushPsbt()` methods.

```javascript
const psbtHex = "70736274ff01..."; // Your PSBT hex string

try {
  const { signedPsbtHex, txId } = await signPsbt(psbtHex, true);
  const txId = await pushPsbt(signedPsbtHex);
  console.log("Transaction ID:", txId);
} catch (error) {
  console.error("Error handling PSBT:", error);
}
```

### Switching Networks

Switch between Bitcoin networks using the `switchNetwork()` method.

```javascript
try {
  await switchNetwork("testnet");
  console.log("Switched to testnet");
} catch (error) {
  console.error("Error switching network:", error);
}
```

### Error Handling

All wallet-related operations should be wrapped in `try-catch` blocks to handle errors gracefully.

```javascript
try {
  await connect(UNISAT);
} catch (error) {
  console.error("Failed to connect:", error);
}
```

## Advanced Usage

### Custom Configuration

Create a custom configuration using `createConfig()`.

```javascript
import { createConfig } from "@omnisat/lasereyes";

const config = createConfig({
  network: "testnet",
  provider: "unisat",
  chainId: 1,
});
```

### Best Practices

- **Check Wallet Connection:** Always verify if the wallet is connected before performing operations.
- **Handle Network Changes:** Update address, balance, and other network-specific data when switching networks.
- **Provide Feedback:** Keep users informed during wallet operations, especially when user interaction is required.
- **Validate User Input:** Always validate addresses and amounts before sending transactions.

## Potential Issues

1. **Wallet Not Installed:** Ensure that the wallet (e.g., Unisat or XVerse`) is installed before attempting to connect.
2. **Network Mismatch:** Switching networks may cause discrepancies in balance and address data. Handle these changes properly.
3. **Transaction Delays:** Bitcoin transactions can take time to confirm on the network, so provide users with updates.

## API Reference

### `LaserEyesProvider`

- **Props:**  
  `config`: `{ network: 'mainnet' | 'testnet' }`

### `useLaserEyes` Hook

- **Properties:**
  - `connected`: Boolean - Whether a wallet is connected. 
  - `balance`: Number - Wallet balance in satoshis.
  - `address`: String - Wallet address.
  - `publicKey`: String - Wallet public key.
  - `provider`: String - Wallet provider name.
  - `network`: `'mainnet' | 'testnet'` - Current network.
  - `accounts`: Array - Wallet accounts.

- **Methods:**
  - `connect(walletName: string)`
  - `disconnect()`
  - `sendBTC(to: string, amount: number)`
  - `signMessage(message: string)`
  - `signPsbt(psbt: string)`
  - `pushPsbt(psbt: string)`
  - `switchNetwork(network: 'mainnet' | 'testnet')`

---

This guide is designed to help you get started with Laser Eyes quickly while also covering more advanced use cases and best practices to ensure a smooth integration. By leveraging the powerful features of Laser Eyes, you can build robust Bitcoin applications that provide seamless wallet interactions for users.

## Learn About PSBT

If you'd like to understand Partially Signed Bitcoin Transactions (PSBT) in more detail, check out this video by Me. This video is part of the submission to the "PSBT Educational Content Challenge" and offers a comprehensive explanation of PSBTs and their usage in Bitcoin development.

[Watch the video here](https://youtu.be/v3oxyi-dGR0)

This educational content will help you gain a deeper understanding of how PSBTs work, and how you can leverage them in your Bitcoin applications.
