# <div align="center">Zero-To-Dapp-Workshop</div>
<div align="center" ><img width="350px" src="https://github.com/eben619/Zero-To-Dapp-Workshop/blob/main/ethAccraHero.png"></div>


### <div>Remix Presentation</div>

<p>https://bit.ly/remixAccra24<p/><br>


### <div>Link To Alfajores Testnet Faucet<div/>
<p>https://faucet.celo.org/alfajores</p><br>


### <div>Link To Spreadsheet Document</div>

<p>https://docs.google.com/spreadsheets/d/1KoaSQbOV7zf04XkPNmIWoOw4bDeOTUASXrl77KfkUOI/edit?usp=sharing<p/><br>

## <div align="center">SESSION ONE (REMIX IDE WORKSHOP)</div>

### <div align="center">OBJECTIVES</div><br>

(i) Adapting existing smart contracts and writing your own<br>

(ii) Compiling the code<br>

(iii) Checking the code for errors and warnings & finding bugs<br>

(iv) Deploying the compiled code on a blockchain<br>

(v) Verifying the contract<br>

(vii) Interacting with your deployed contracts<br>

(viii) Writing our own ERC 721 (NFT) contracts and deploying it.<br>

(ix) Importing the NFT into Metamask.

## <div align="center">SESSION TWO (CELO WORKSHOP)</div>

(i) Intro to Solidity.<br>

    - Data types (Value types, Reference types,Other Types.. etc)
    - Structure of a function in Solidity
    - Visibility of functions.
    - Inheritance.
    
***Basic Structure Of A function***<br>
<img src="https://github.com/eben619/Celo_Africa_Dao-Ghana_University_Tour/blob/main/function.avif" width="500px">
    
(ii) Writing a basic Escrow smart contract in Solidity<br>

    - Explaining the escrow smart contract's syntax.

(iii) Introduction To Celo Composer<br>

    - Create a new project with the celo composer. 
    - Explain Celo Composer Templates. (Valora, MiniPay, Social Connect)
    - Create a frontend for the Escrow Smart contract with Celo Composer.
    - Handle smart contract interactions.
    - How to add more chains to Celo Composer.
    - Fully functioning Escrow Dapp.

## <div align="center"> Resource Materials </div>

Celo Composer allows you to quickly build, deploy, and iterate on decentralized applications using Celo. It provides a number of frameworks, examples, and Celo specific functionality to help you get started with your next dApp.

## Prerequisites

- Node (v20 or higher)
- Git (v2.38 or higher)

<details>
  <summary>HOW TO USE CELO COMPOSER</summary><br>

  The easiest way to start with Celo Composer is by using `@celo/celo-composer`. This CLI tool lets you quickly start building dApps on Celo for multiple frameworks, including React (with either react-celo or rainbowkit-celo), React Native (w/o Expo), Flutter, and Angular. To get started, just run the following command, and follow the steps:

- Step 1

```bash
npx @celo/celo-composer@latest create
```

- Step 2: Provide the Project Name: You will be prompted to enter the name of your project.

```text
What is your project name: 
```

- Step 3: Choose to Use Hardhat: You will be asked if you want to use Hardhat. Select Yes or No.

```text
Do you want to use Hardhat? (Y/n)
```

- Step 4: Choose to Use a Template: You will be asked if you want to use a template. Select `Yes` or `No`.

```text
Do you want to use a template?
```

- Step 5: Select a Template: If you chose to use a template, you will be prompted to select a template from the list provided.

```text
- Minipay
- Valora
- Social Connect
```

- Step 6: Provide the Project Owner's Name: You will be asked to enter the project owner's name.

```text
Project Owner name:
```

- Step 7: Wait for Project Creation: The CLI will now create the project based on your inputs. This may take a few minutes.

- Step 8: Follow the instructions to start the project. The same will be displayed on the console after the project is created.

```text
🚀 Your starter project has been successfully created!

Before you start the project, please follow these steps:

1. Rename the file:
   packages/react-app/.env.template
   to
   packages/react-app/.env

2. Open the newly renamed .env file and add all the required environment variables.

Once you've done that, you're all set to start your project!

Run the following commands from the packages/react-app folder to start the project:

   yarn install
   yarn react-app:dev
```
</details>

<details>
  <summary>ESCROW SMART CONTRACT</summary><br>

  ```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Define the smart contract
contract Escrow {

    // Enum to represent the different states of the escrow process
    enum State { AWAITING_PAYMENT, AWAITING_DELIVERY, COMPLETE, REFUNDED }

    // Public state variable to store the address of the buyer
    address public buyer;

    // Public state variable to store the payable address of the seller
    address payable public seller;
    
    // Public state variable to store the address of the escrow agent (trusted third party)
    address public escrowAgent;

    // Public state variable to track the current state of the escrow
    State public currentState;

    // Constructor function to initialize the contract with the buyer, seller, and escrow agent
    constructor(address _buyer, address payable _seller) {
        buyer = _buyer; // Set the buyer's address
        seller = _seller; // Set the seller's payable address
        escrowAgent = msg.sender; // The deployer of the contract becomes the escrow agent
        currentState = State.AWAITING_PAYMENT; // Initialize the escrow state to 'AWAITING_PAYMENT'
    }

    // Modifier to restrict access to functions only to the buyer
    modifier onlyBuyer() {
        require(msg.sender == buyer, "Only the buyer can call this function.");
        _;
    }

    // Modifier to restrict access to functions only to the escrow agent
    modifier onlyEscrowAgent() {
        require(msg.sender == escrowAgent, "Only the escrow agent can call this function.");
        _;
    }

    // Modifier to ensure that the function is called only when the contract is in the expected state
    modifier inState(State expectedState) {
        require(currentState == expectedState, "Invalid state.");
        _;
    }

    // Function for the buyer to deposit funds into the escrow
    function deposit() external payable onlyBuyer inState(State.AWAITING_PAYMENT) {
        require(msg.value > 0, "Deposit must be greater than 0."); // Ensure the deposit amount is greater than 0
        currentState = State.AWAITING_DELIVERY; // Update the state to 'AWAITING_DELIVERY'
    }

    // Function for the buyer to confirm delivery and release funds to the seller
    function confirmDelivery() external onlyBuyer inState(State.AWAITING_DELIVERY) {
        currentState = State.COMPLETE; // Update the state to 'COMPLETE'
        seller.transfer(address(this).balance); // Transfer the escrowed funds to the seller
    }

    // Function for the escrow agent to refund the buyer if conditions are not met
    function refund() external onlyEscrowAgent inState(State.AWAITING_DELIVERY) {
        currentState = State.REFUNDED; // Update the state to 'REFUNDED'
        payable(buyer).transfer(address(this).balance); // Refund the escrowed funds to the buyer
    }
}


```

</details>

<details>
  <summary>
SYNTAX EXPLANATION
  </summary><br>

  ***SUMMARY***

  This contract effectively implements a basic escrow mechanism with three main participants: the buyer, the seller, and the escrow agent. It uses the State enum to track the progress of the transaction and various modifiers to enforce rules about who can call certain functions and when. The contract ensures that funds are securely held and only released based on specific actions by the buyer or the escrow agent, thereby providing a secure method for handling transactions that require an escrow.<br>


  ***License Identifier:***  The line // SPDX-License-Identifier: MIT specifies that this contract is licensed under the MIT license, which is a permissive free software license.


***Pragma Directive:*** pragma solidity ^0.8.0; declares that the contract is written for Solidity version 0.8.0 or higher, but not including version 0.9.0.


***Contract Declaration:***
    contract Escrow defines a new smart contract named Escrow.
    

***Enum State***

    `enum State { AWAITING_PAYMENT, AWAITING_DELIVERY, COMPLETE, REFUNDED } defines a custom type with four possible values: AWAITING_PAYMENT,           AWAITING_DELIVERY, COMPLETE, and REFUNDED. This enum helps manage the different stages of the escrow process.`
    

***State Variables***

        address public buyer;: This is a public state variable that holds the Ethereum address of the buyer.

        address payable public seller;: This is a public state variable that holds the Ethereum address of the seller, marked as payable because it will receive Ether.

        address public escrowAgent;: This is a public state variable that holds the Ethereum address of the escrow agent, who acts as a trusted third party.

        State public currentState;: This is a public state variable of type State that holds the current state of the escrow transaction.
        


***Constructor:*** The constructor is a special function that is executed only once when the contract is deployed. It initializes the contract with specific values:

        buyer = _buyer;: Sets the buyer's address to the value provided in _buyer parameter.

        seller = _seller;: Sets the seller's payable address to the value provided in _seller parameter.

        escrowAgent = msg.sender;: Assigns the deployer of the contract as the escrow agent.

        currentState = State.AWAITING_PAYMENT;  Sets the initial state of the escrow to AWAITING_PAYMENT, indicating that the contract is waiting for the buyer to deposit funds.
        

***Modifiers***

    onlyBuyer: This modifier restricts access to a function, ensuring that only the buyer can call it. It checks if msg.sender (the address that called the function) is equal to the buyer's address.

    onlyEscrowAgent: This modifier restricts access to a function, ensuring that only the escrow agent can call it. It checks if msg.sender is equal to the escrow agent's address.

    inState: This modifier restricts function execution based on the current state of the contract. It ensures that the contract is in the specified expectedState before allowing the function to run.
    
        
***Deposit Function:***  This function allows the buyer to deposit Ether into the escrow contract.

    external: Specifies that this function can be called from outside the contract.

    payable: Indicates that the function can receive Ether.

    onlyBuyer: Ensures that only the buyer can call this function.

    inState(State.AWAITING_PAYMENT): Ensures that the contract is currently in the AWAITING_PAYMENT state.

    require(msg.value > 0, "Deposit must be greater than 0.");: This check ensures that the deposited amount is greater than zero.

    currentState = State.AWAITING_DELIVERY;: After a successful deposit, the contract state is updated to AWAITING_DELIVERY.

***confirmDelivery Function:***  This function allows the buyer to confirm the delivery of goods or services, which releases the escrowed funds to the seller.

    external: Specifies that this function can be called from outside the contract.

    onlyBuyer: Ensures that only the buyer can call this function.

    inState(State.AWAITING_DELIVERY): Ensures that the contract is currently in the AWAITING_DELIVERY state.

    currentState = State.COMPLETE;: Updates the contract state to COMPLETE.

    seller.transfer(address(this).balance);: Transfers all Ether held in the contract to the seller.


***refund Function:***  This function allows the escrow agent to refund the buyer if the delivery conditions are not met.

    external: Specifies that this function can be called from outside the contract.

    onlyEscrowAgent: Ensures that only the escrow agent can call this function.

    inState(State.AWAITING_DELIVERY): Ensures that the contract is currently in the AWAITING_DELIVERY state.

    currentState = State.REFUNDED;: Updates the contract state to REFUNDED.

    payable(buyer).transfer(address(this).balance);: Transfers all Ether held in the contract back to the buyer.

</details>


<details>
  <summary>
FRONTEND FOR ESCROW SMART CONTRACT
  </summary><br>
    
 ***Install web3.js***
```
yarn add web3

```
1)***Deploy your smart contract and copy the contract ABI*** <br>

2)***Copy your smart contract address*** <br>

3)***Create an EscrowComponent.tsx file under the component folder***

4)***Import the EscrowComponent.tsx component into the index.tsx file under the pages folder***

```
    import EscrowComponent from '@/components/EscrowComponent';
```

```
import React, { useState, useEffect } from 'react';
import Web3 from 'web3';

// Replace with your deployed contract's ABI and address
const escrowContractABI = [/* ABI Array Here */]; // The ABI (Application Binary Interface) is required to interact with the smart contract
const escrowContractAddress = '0xYourContractAddressHere'; // The deployed contract's address on the blockchain

const EscrowComponent: React.FC = () => {
  // State hooks to manage web3 instance, user account, contract instance, and transaction status
  const [web3, setWeb3] = useState<Web3 | null>(null);
  const [account, setAccount] = useState<string | null>(null);
  const [contract, setContract] = useState<any>(null);
  const [status, setStatus] = useState<string>('');

  // useEffect hook to initialize web3 and set up the contract instance when the component mounts
  useEffect(() => {
    const initWeb3 = async () => {
      if ((window as any).ethereum) { // Check if MetaMask or another web3 provider is installed
        try {
          const web3Instance = new Web3((window as any).ethereum); // Create a new instance of web3 with the provider
          setWeb3(web3Instance); // Save the web3 instance in state
          
          const accounts = await web3Instance.eth.requestAccounts(); // Request accounts from MetaMask
          setAccount(accounts[0]); // Set the first account as the current user account

          const escrowInstance = new web3Instance.eth.Contract(
            escrowContractABI, // The ABI of the contract
            escrowContractAddress // The address where the contract is deployed
          );
          setContract(escrowInstance); // Save the contract instance in state
        } catch (error) {
          console.error('Error initializing web3:', error); // Log any errors that occur during web3 initialization
        }
      } else {
        alert('Please install MetaMask!'); // Alert the user if MetaMask is not installed
      }
    };

    initWeb3(); // Call the function to initialize web3
  }, []); // Empty dependency array means this useEffect runs only once when the component mounts

  // Function to deposit funds into the escrow contract
  const depositFunds = async () => {
    if (web3 && contract && account) { // Ensure web3, contract, and account are available
      try {
        await contract.methods.deposit().send({
          from: account, // The transaction is sent from the user's account
          value: web3.utils.toWei('1', 'ether'), // The amount of Ether to deposit, converted to Wei
        });
        setStatus('Funds deposited successfully.'); // Update the status message on successful deposit
      } catch (error) {
        console.error('Deposit failed:', error); // Log any errors that occur during deposit
        setStatus('Deposit failed.'); // Update the status message on deposit failure
      }
    }
  };

  // Function to confirm delivery of goods/services in the escrow contract
  const confirmDelivery = async () => {
    if (web3 && contract && account) { // Ensure web3, contract, and account are available
      try {
        await contract.methods.confirmDelivery().send({ from: account }); // Call the confirmDelivery method on the contract
        setStatus('Delivery confirmed successfully.'); // Update the status message on successful confirmation
      } catch (error) {
        console.error('Confirmation failed:', error); // Log any errors that occur during confirmation
        setStatus('Confirmation failed.'); // Update the status message on confirmation failure
      }
    }
  };

  // Function to request a refund from the escrow contract
  const requestRefund = async () => {
    if (web3 && contract && account) { // Ensure web3, contract, and account are available
      try {
        await contract.methods.refund().send({ from: account }); // Call the refund method on the contract
        setStatus('Refund requested successfully.'); // Update the status message on successful refund request
      } catch (error) {
        console.error('Refund failed:', error); // Log any errors that occur during refund request
        setStatus('Refund failed.'); // Update the status message on refund failure
      }
    }
  };

  // JSX for the component's UI
  return (
    <div className="p-4">
      <h2 className="text-xl mb-4">Escrow Contract Interaction</h2>
      <p>Status: {status}</p> {/* Display the current status message */}
      {account ? ( // Check if the user's account is connected
        <div>
          <button
            onClick={depositFunds} // Attach the depositFunds function to the button's onClick event
            className="bg-blue-500 text-white p-2 m-2 rounded"
          >
            Deposit Funds
          </button>
          <button
            onClick={confirmDelivery} // Attach the confirmDelivery function to the button's onClick event
            className="bg-green-500 text-white p-2 m-2 rounded"
          >
            Confirm Delivery
          </button>
          <button
            onClick={requestRefund} // Attach the requestRefund function to the button's onClick event
            className="bg-red-500 text-white p-2 m-2 rounded"
          >
            Request Refund
          </button>
        </div>
      ) : (
        <p>Please connect to MetaMask.</p> // Prompt the user to connect to MetaMask if not connected
      )}
    </div>
  );
};

export default EscrowComponent; // Export the component for use in other parts of the application

```
</details><br><br>

<div align="center" >SUPPORT (Join The Celo Ghana Developpers Community)</div><br>

<div align="center" ><img width="350px" src="https://github.com/eben619/Ho_Code-Jams/blob/main/CeloGhanaCommunity.jpg"></div>


    







