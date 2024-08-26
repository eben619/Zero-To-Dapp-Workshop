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
