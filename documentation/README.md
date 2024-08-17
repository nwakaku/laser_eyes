# Laser Eyes Integration Guide for Developers
# By Wisdom Chris

## Table of Contents
1. [Introduction](#introduction)
2. [Installation](#installation)
3. [Basic Setup](#basic-setup)
4. [Connecting to a Wallet](#connecting-to-a-wallet)
5. [Handling Wallet State](#handling-wallet-state)
6. [Sending Bitcoin](#sending-bitcoin)
7. [Signing Messages](#signing-messages)
8. [Working with PSBTs](#working-with-psbts)
9. [Network Switching](#network-switching)
10. [Error Handling](#error-handling)
11. [Best Practices](#best-practices)
12. [Common Issues and Troubleshooting](#common-issues-and-troubleshooting)

## 1. Introduction

Laser Eyes is a React library that simplifies Bitcoin wallet integration in web applications. This guide will walk you through the process of integrating Laser Eyes into your project, covering basic setup, wallet interactions, and advanced features.

## 2. Installation

To get started with Laser Eyes, install it using your preferred package manager:

```bash
npm install @omnisat/lasereyes
# or
yarn add @omnisat/lasereyes
# or
pnpm add @omnisat/lasereyes
```

## 3. Basic Setup

First, wrap your app with the `LaserEyesProvider`:

```jsx
import { LaserEyesProvider, MAINNET } from "@omnisat/lasereyes";

function App() {
  return (
    <LaserEyesProvider config={{ network: MAINNET }}>
      {/* Your app components */}
    </LaserEyesProvider>
  );
}
```

Note: If you're using server-side rendering (SSR), make sure to render the `LaserEyesProvider` only on the client-side:

```jsx
import { useEffect, useState } from "react";
import { LaserEyesProvider, MAINNET } from "@omnisat/lasereyes";

function App() {
  const [isClient, setIsClient] = useState(false);

  useEffect(() => {
    setIsClient(true);
  }, []);

  if (!isClient) return null;

  return (
    <LaserEyesProvider config={{ network: MAINNET }}>
      {/* Your app components */}
    </LaserEyesProvider>
  );
}
```

## 4. Connecting to a Wallet

Use the `useLaserEyes` hook to access wallet functions in your components:

```jsx
import { useLaserEyes, UNISAT } from "@omnisat/lasereyes";

function WalletConnector() {
  const { connect, connected, hasUnisat } = useLaserEyes();

  const handleConnect = async () => {
    if (hasUnisat) {
      try {
        await connect(UNISAT);
        console.log("Connected to Unisat wallet");
      } catch (error) {
        console.error("Failed to connect:", error);
      }
    } else {
      console.log("Unisat wallet is not installed");
    }
  };

  return (
    <button onClick={handleConnect} disabled={connected}>
      {connected ? "Connected" : "Connect Wallet"}
    </button>
  );
}
```

## 5. Handling Wallet State

After connecting, you can access various wallet properties:

```jsx
function WalletInfo() {
  const { connected, address, balance, network } = useLaserEyes();

  if (!connected) return <p>Wallet not connected</p>;

  return (
    <div>
      <p>Address: {address}</p>
      <p>Balance: {balance} satoshis</p>
      <p>Network: {network}</p>
    </div>
  );
}
```

## 6. Sending Bitcoin

To send Bitcoin, use the `sendBTC` function:

```jsx
function SendBitcoin() {
  const { sendBTC } = useLaserEyes();
  const [recipient, setRecipient] = useState("");
  const [amount, setAmount] = useState(0);

  const handleSend = async () => {
    try {
      const txId = await sendBTC(recipient, amount);
      console.log("Transaction sent. ID:", txId);
    } catch (error) {
      console.error("Error sending BTC:", error);
    }
  };

  return (
    <div>
      <input
        type="text"
        value={recipient}
        onChange={(e) => setRecipient(e.target.value)}
        placeholder="Recipient address"
      />
      <input
        type="number"
        value={amount}
        onChange={(e) => setAmount(Number(e.target.value))}
        placeholder="Amount in satoshis"
      />
      <button onClick={handleSend}>Send BTC</button>
    </div>
  );
}
```

## 7. Signing Messages

To sign messages with the connected wallet:

```jsx
function MessageSigner() {
  const { signMessage } = useLaserEyes();
  const [message, setMessage] = useState("");
  const [signature, setSignature] = useState("");

  const handleSign = async () => {
    try {
      const sig = await signMessage(message);
      setSignature(sig);
    } catch (error) {
      console.error("Error signing message:", error);
    }
  };

  return (
    <div>
      <input
        type="text"
        value={message}
        onChange={(e) => setMessage(e.target.value)}
        placeholder="Message to sign"
      />
      <button onClick={handleSign}>Sign Message</button>
      {signature && <p>Signature: {signature}</p>}
    </div>
  );
}
```

## 8. Working with PSBTs

For more advanced transactions, you can work with Partially Signed Bitcoin Transactions (PSBTs):

```jsx
function PsbtSigner() {
  const { signPsbt, pushPsbt } = useLaserEyes();
  const [psbt, setPsbt] = useState("");
  const [signedPsbt, setSignedPsbt] = useState("");
  const [txId, setTxId] = useState("");

  const handleSignPsbt = async () => {
    try {
      const result = await signPsbt(psbt, true);
      setSignedPsbt(result.signedPsbtHex || "");
    } catch (error) {
      console.error("Error signing PSBT:", error);
    }
  };

  const handlePushPsbt = async () => {
    try {
      const result = await pushPsbt(signedPsbt);
      setTxId(result || "");
    } catch (error) {
      console.error("Error pushing PSBT:", error);
    }
  };

  return (
    <div>
      <textarea
        value={psbt}
        onChange={(e) => setPsbt(e.target.value)}
        placeholder="Enter PSBT"
      />
      <button onClick={handleSignPsbt}>Sign PSBT</button>
      {signedPsbt && (
        <>
          <p>Signed PSBT: {signedPsbt}</p>
          <button onClick={handlePushPsbt}>Push PSBT</button>
        </>
      )}
      {txId && <p>Transaction ID: {txId}</p>}
    </div>
  );
}
```

## 9. Network Switching

To switch between mainnet and testnet:

```jsx
function NetworkSwitcher() {
  const { switchNetwork, network } = useLaserEyes();

  const handleSwitch = async (newNetwork) => {
    try {
      await switchNetwork(newNetwork);
      console.log(`Switched to ${newNetwork}`);
    } catch (error) {
      console.error("Error switching network:", error);
    }
  };

  return (
    <div>
      <p>Current network: {network}</p>
      <button onClick={() => handleSwitch("mainnet")}>Switch to Mainnet</button>
      <button onClick={() => handleSwitch("testnet")}>Switch to Testnet</button>
    </div>
  );
}
```

## 10. Error Handling

Always wrap asynchronous operations in try-catch blocks to handle potential errors:

```jsx
try {
  // Asynchronous operation
  await someAsyncFunction();
} catch (error) {
  console.error("An error occurred:", error);
  // Handle the error appropriately (e.g., show an error message to the user)
}
```

## 11. Best Practices

1. Always check if a wallet is connected before performing operations.
2. Provide clear feedback to users during wallet operations.
3. Validate user input before sending transactions or signing messages.
4. Use appropriate error handling for all asynchronous operations.
5. Keep sensitive information (like private keys) secure and never expose them in your application.
6. Regularly update the Laser Eyes library to ensure you have the latest features and security improvements.

## 12. Common Issues and Troubleshooting

- **Wallet not connecting**: Ensure the user has the wallet extension installed and it's up to date.
- **Network mismatch**: Make sure your app and the wallet are on the same network (mainnet or testnet).
- **Insufficient funds**: Check the wallet balance before attempting to send transactions.
- **User rejection**: Handle cases where the user may reject a transaction or signature request in their wallet.

By following this guide, you should be able to successfully integrate Laser Eyes into your React application and work with Bitcoin wallets effectively. Remember to always prioritize security and provide a smooth user experience when dealing with cryptocurrency transactions.
