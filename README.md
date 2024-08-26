# <div align="center">Zero-To-Dapp-Workshop</div>
<div align="center" ><img width="350px" src="https://github.com/eben619/Zero-To-Dapp-Workshop/blob/main/ethAccraHero.png"></div>


### <div>Link To Remix Presentation</div>

<p>https://bit.ly/remixAccra24<p/><br>

### <div>Link To Sepolia Testnet Faucet</div>
<p>https://cloud.google.com/application/web3/faucet/ethereum/sepolia</p><br>

### <div>Link To Alfajores Testnet Faucet<div/>
<p>https://faucet.celo.org/alfajores</p><br>

## <div align="center">SESSION ONE (REMIX IDE WORKSHOP)</div>

### <div align="center">OBJECTIVES</div><br>

(i) Adapting existing smart contracts and writing your own<br>

(ii) Compile the code<br>

(iii) Checking the code for errors and warnings & finding bugs<br>

(iv) Deploying the compiled code on a blockchain<br>

(v) Verifying the contract<br>

(vii) Interacting with deployed contracts<br>

(viii) Writing our own ERC 721 (NFT) contracts and deploying it.<br>

(ix) Importing the NFT into Metamask.

## <div align="center">SESSION TWO (CELO WORKSHOP)</div>

(i) Intro to Solidity.<br>

    - Data types (Value types, Reference types.. etc)
    - Structure of a function in Solidity
    - Visibility of functions.
    - Inheritance.
    
(ii) Writing a basic Escrow smart contract in Solidity.<br>

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

## How to use Celo Composer

The easiest way to start with Celo Composer is using `@celo/celo-composer`. This CLI tool lets you quickly start building dApps on Celo for multiple frameworks, including React (with either react-celo or rainbowkit-celo), React Native (w/o Expo), Flutter, and Angular. To get started, just run the following command, and follow the steps:

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


#  <div> Escrow Smart Contract</div>

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

## <div>SYNTAX EXPLANATION</div>

<p>
License Identifier: The line // SPDX-License-Identifier: MIT specifies that this contract is licensed under the MIT license, which is a permissive free software license.

Pragma Directive: pragma solidity ^0.8.0; declares that the contract is written for Solidity version 0.8.0 or higher, but not including version 0.9.0.

Contract Declaration:
    contract Escrow defines a new smart contract named Escrow.

Enum State

    `enum State { AWAITING_PAYMENT, AWAITING_DELIVERY, COMPLETE, REFUNDED } defines a custom type with four possible values: AWAITING_PAYMENT,           AWAITING_DELIVERY, COMPLETE, and REFUNDED. This enum helps manage the different stages of the escrow process.`

State Variables

        address public buyer;: This is a public state variable that holds the Ethereum address of the buyer.

        address payable public seller;: This is a public state variable that holds the Ethereum address of the seller, marked as payable because it will receive Ether.

        address public escrowAgent;: This is a public state variable that holds the Ethereum address of the escrow agent, who acts as a trusted third party.

        State public currentState;: This is a public state variable of type State that holds the current state of the escrow transaction.


Constructor: The constructor is a special function that is executed only once when the contract is deployed. It initializes the contract with specific values:

        buyer = _buyer;: Sets the buyer's address to the value provided in _buyer parameter.

        seller = _seller;: Sets the seller's payable address to the value provided in _seller parameter.

        escrowAgent = msg.sender;: Assigns the deployer of the contract as the escrow agent.

        currentState = State.AWAITING_PAYMENT;: Sets the initial state of the escrow to AWAITING_PAYMENT, indicating that the contract is waiting for the buyer to deposit funds.
        


    
</p>
