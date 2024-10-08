# Tonkeeper Integration with JavaScript 🚀

Welcome to the **Tonkeeper Integration** project! This repository demonstrates how to seamlessly integrate the **Tonkeeper** wallet into a JavaScript web application using the **TON Connect UI** library. This integration enables users to connect their wallet, retrieve essential information like balance and address, and send transactions using **Toncoin**.

## Features ✨

- **🔗 Connect to Tonkeeper Wallet**: Users can securely connect their Tonkeeper wallet to the web application.
- **📊 Fetch Wallet Information**: Retrieve and display wallet details such as the connected wallet's address and balance.
- **💸 Send Transactions**: Send Toncoin transactions directly from the web application.
- **🚪 Disconnect Wallet**: Users can easily disconnect their wallet and reset the session.

## Prerequisites 📋

Before you get started, ensure you have the following:

- A valid domain or local server to host the HTML file.
- A **manifest.json** file hosted at a publicly accessible URL, which is required for TON Connect.

### Manifest File Example (tonconnect-manifest.json) 📄

```json
{
  "name": "Tonkeeper DApp",
  "description": "A sample decentralized app integrated with Tonkeeper using TON Connect.",
  "url": "https://miladsoft.github.io/ton-connect",
  "iconUrl": "https://your-domain.com/icon.png",
  "bridgeUrl": "https://bridge.tonapi.io/bridge",
  "termsUrl": "https://your-domain.com/terms",
  "privacyUrl": "https://your-domain.com/privacy"
}
```

### What's in the Manifest? 🧐
- **name**: The name of your application.
- **description**: A brief description of your app.
- **url**: The URL of your application.
- **iconUrl**: The URL of the icon that will be displayed in the wallet.
- **bridgeUrl**: The bridge URL used for communication between the app and the wallet.
- **termsUrl**: A link to the terms and conditions of your application.
- **privacyUrl**: A link to your privacy policy.

Make sure you host this file on a server and update the `manifestUrl` in the project accordingly.

## Getting Started 🚀

### 1. Clone the repository

```bash
git clone https://github.com/miladsoft/ton-connect.git
```

### 2. Host the project

You can either host the project locally or deploy it on a web server. If you're testing locally, use a development server such as:

- **Live Server** (for Visual Studio Code)
- **http-server** via npm

### 3. Update `tonconnect-manifest.json`

Ensure that the URL for the manifest file is correct in the code. Update the `manifestUrl` in the script section of the HTML file if necessary.

```javascript
const tonConnectUI = new TON_CONNECT_UI.TonConnectUI({
  manifestUrl: 'https://miladsoft.github.io/ton-connect/tonconnect-manifest.json',
  buttonRootId: 'ton-connect',
});
```

### 4. Open the HTML file

After hosting the file, open it in a browser and use it to connect to your Tonkeeper wallet, check wallet details, and send transactions.

## How It Works 🔍

### 1. **Connecting to Tonkeeper**

When the page loads, the app attempts to connect to Tonkeeper using the TON Connect UI. A "Connect" button is rendered, allowing the user to initiate the connection.

```javascript
const tonConnectUI = new TON_CONNECT_UI.TonConnectUI({
  manifestUrl: 'https://miladsoft.github.io/ton-connect/tonconnect-manifest.json',
  buttonRootId: 'ton-connect',
});
```

Once the user clicks the button and connects the wallet, the application retrieves the wallet information (address and balance) and displays it.

### 2. **Displaying Wallet Information**

After connecting, the wallet address and balance are fetched using the **TON API**. The balance is displayed in the wallet info section.

```javascript
const balanceResponse = await fetch(`https://tonapi.io/v2/accounts/${connectedWallet.account.address}`);
const balanceData = await balanceResponse.json();
balanceElem.textContent = balanceData.balance || "0";
```

### 3. **Sending Transactions**

The application provides a "Send Transaction" button that, when clicked, sends Toncoin to a predefined destination address.

```javascript
const transaction = {
  messages: [
    {
      address: "UQDtXe2aK_1i5BzKdaRi0LseTW8PbuvrCcX7qfREHhJ0typh", // destination address
      amount: "20000000", // Toncoin in nanotons
    },
  ],
};

const result = await tonConnectUI.sendTransaction(transaction);
```

### 4. **Disconnecting the Wallet**

Users can disconnect their wallet using the "Disconnect" button. This action will hide the wallet information and disable the transaction button.

```javascript
disconnectBtn.addEventListener("click", async () => {
  await tonConnectUI.disconnect();
  walletInfoDiv.style.display = "none";
  sendTransactionBtn.style.display = "none";
  console.log("Disconnected");
});
```

## Full Code Example 💻

Here is the full HTML file:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Tonkeeper Integration</title>
    <script src="https://unpkg.com/@tonconnect/ui@latest/dist/tonconnect-ui.min.js"></script>

    <style>
      body {
        font-family: Arial, sans-serif;
        margin: 20px;
      }
      #ton-connect {
        position: fixed;
        top: 20px;
        right: 20px;
        margin-top: 0;
      }

      .wallet-info {
        margin-top: 20px;
        border: 1px solid #ddd;
        padding: 10px;
        border-radius: 5px;
      }
      button {
        padding: 10px 20px;
        font-size: 16px;
        cursor: pointer;
        margin-top: 10px;
      }
    </style>
  </head>
  <body>
    <h1>Tonkeeper Integration with JavaScript</h1>

    <div id="ton-connect"></div>

    <div id="wallet-info" class="wallet-info" style="display: none">
      <h3>Wallet Information</h3>
      <p><strong>Address:</strong> <span id="address"></span></p>
      <p><strong>Balance:</strong> <span id="balance"></span></p>
      <button id="disconnect">Disconnect</button>
    </div>

    <button id="send-transaction" style="display: none">
      Send Transaction
    </button>

    <script>
      const tonConnectUI = new TON_CONNECT_UI.TonConnectUI({
        manifestUrl:
          "https://miladsoft.github.io/ton-connect/tonconnect-manifest.json",
        buttonRootId: "ton-connect",
      });

      const addressElem = document.getElementById("address");
      const balanceElem = document.getElementById("balance");
      const walletInfoDiv = document.getElementById("wallet-info");
      const sendTransactionBtn = document.getElementById("send-transaction");
      const disconnectBtn = document.getElementById("disconnect");

      async function connectToWallet() {
        try {
          const connectedWallet = await tonConnectUI.connectWallet();
          console.log("Connected Wallet:", connectedWallet);

          walletInfoDiv.style.display = "block";
          addressElem.textContent = connectedWallet.account.address;

          const balanceResponse = await fetch(
            `https://tonapi.io/v2/accounts/${connectedWallet.account.address}`
          );
          const balanceData = await balanceResponse.json();
          balanceElem.textContent = balanceData.balance || "0";

          sendTransactionBtn.style.display = "block";
        } catch (error) {
          console.error("Error connecting to wallet:", error);
        }
      }

      disconnectBtn.addEventListener("click", async () => {
        await tonConnectUI.disconnect();
        walletInfoDiv.style.display = "none";
        sendTransactionBtn.style.display = "none";
        console.log("Disconnected");
      });

      sendTransactionBtn.addEventListener("click", async () => {
        const transaction = {
          messages: [
            {
              address: "UQDtXe2aK_1i5BzKdaRi0LseTW8PbuvrCcX7qfREHhJ0typh", 
              amount: "20000000", 
            },
          ],
        };

        try {
          const result = await tonConnectUI.sendTransaction(transaction);
          console.log("Transaction Result:", result);
          alert("Transaction sent successfully!");
        } catch (error) {
          console.error("Transaction failed:", error);
          alert("Transaction failed!");
        }
      });

      window.onload = connectToWallet;
    </script>
  </body>
</html>
```

## Future Enhancements 🚀

- **Token Support**: Extend the project to retrieve and display token balances.
- **Multi-Signature Wallets**: Add support for multi-signature wallets and related transaction types.
- **Improved UX**: Enhance user experience by adding loading spinners, better error handling, and real-time balance updates.

## Contributing 🙌

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page