# Decentralized Election System

## Overview
The **Decentralized Election System** is a blockchain-based voting platform that ensures transparency, security, and immutability. It allows voters to cast their votes securely, preventing fraud and manipulation. The system leverages **Ethereum smart contracts** to manage elections in a decentralized manner.

## Features
- 🗳 **Tamper-proof Voting:** Votes are recorded on the blockchain, ensuring transparency.
- 🔒 **Secure and Private:** Each voter is authenticated and can vote only once.
- ⏳ **Real-time Results:** Election results are publicly verifiable.
- 🎛 **Admin Privileges:** Admins can add candidates and start elections.
- 🌍 **Decentralized:** No central authority controls the election process.

## Tech Stack
- **Solidity** (Smart Contract Development)
- **Ethereum & Hardhat** (Blockchain & Deployment)
- **React.js + Ethers.js** (Frontend Interface)
- **MetaMask** (Wallet & Authentication)
- **IPFS** (For storing candidate details, optional)

---
## Setup & Installation

### 1. Clone the Repository
```sh
git clone https://github.com/RudraniSingh100/Decentralised-Election-System.git
cd Decentralised-Election-System
```

### 2. Install Dependencies
```sh
npm install
```

### 3. Configure Environment Variables
Create a `.env` file in the root directory and add the following:
```sh
ALCHEMY_API_URL=your_alchemy_or_infura_url
PRIVATE_KEY=your_wallet_private_key
```

---
## Smart Contract Deployment

### 1. Compile Contracts
```sh
npx hardhat compile
```

### 2. Deploy to Ethereum Testnet
```sh
npx hardhat run scripts/deploy.js --network goerli
```
After successful deployment, note the **contract address** and update it in the frontend.

---
## Running the Frontend

### 1. Start Development Server
```sh
cd frontend
npm start
```

### 2. Connect Wallet & Vote
- Open [http://localhost:3000](http://localhost:3000)
- Connect MetaMask and participate in the election.

---
## Smart Contract Overview (Solidity)
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract Election {
    struct Candidate {
        string name;
        uint voteCount;
    }
    
    address public owner;
    Candidate[] public candidates;
    mapping(address => bool) public hasVoted;
    bool public electionActive;
    
    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorized");
        _;
    }
    
    constructor(string[] memory _candidateNames) {
        owner = msg.sender;
        electionActive = true;
        
        for (uint i = 0; i < _candidateNames.length; i++) {
            candidates.push(Candidate({ name: _candidateNames[i], voteCount: 0 }));
        }
    }
    
    function vote(uint candidateIndex) external {
        require(electionActive, "Election is closed");
        require(!hasVoted[msg.sender], "You have already voted");
        require(candidateIndex < candidates.length, "Invalid candidate");
        
        hasVoted[msg.sender] = true;
        candidates[candidateIndex].voteCount++;
    }
    
    function endElection() external onlyOwner {
        electionActive = false;
    }
    
    function getCandidates() external view returns (Candidate[] memory) {
        return candidates;
    }
}
```

---
## Frontend Overview (React + Ethers.js)

### Connecting Wallet & Voting
```javascript
import React, { useState, useEffect } from "react";
import { ethers } from "ethers";
import Web3Modal from "web3modal";
import electionABI from "./ElectionABI.json";

const contractAddress = "YOUR_DEPLOYED_CONTRACT_ADDRESS";

function App() {
    const [provider, setProvider] = useState(null);
    const [contract, setContract] = useState(null);
    const [candidates, setCandidates] = useState([]);
    const [voted, setVoted] = useState(false);

    useEffect(() => {
        loadBlockchainData();
    }, []);

    const loadBlockchainData = async () => {
        const web3Modal = new Web3Modal();
        const connection = await web3Modal.connect();
        const web3Provider = new ethers.providers.Web3Provider(connection);
        const signer = web3Provider.getSigner();
        const electionContract = new ethers.Contract(contractAddress, electionABI, signer);

        setProvider(web3Provider);
        setContract(electionContract);

        const candidatesData = await electionContract.getCandidates();
        setCandidates(candidatesData);
    };

    const vote = async (index) => {
        if (!contract) return;
        try {
            const tx = await contract.vote(index);
            await tx.wait();
            setVoted(true);
        } catch (error) {
            console.error("Error voting:", error);
        }
    };

    return (
        <div>
            <h1>Decentralized Election System</h1>
            <ul>
                {candidates.map((candidate, index) => (
                    <li key={index}>
                        {candidate.name} - Votes: {candidate.voteCount.toString()}
                        {!voted && <button onClick={() => vote(index)}>Vote</button>}
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default App;
```

---
## Future Enhancements
🚀 **Planned Improvements:**
- Integrate **IPFS** for candidate information storage.
- Implement **ZK-SNARKs** for anonymous voting.
- Improve **UI/UX** using TailwindCSS.
- Utilize **The Graph** for efficient vote tracking.

---
## License
This project is **open-source** under the MIT License.

## Contributors
- **Rudrani Singh** - [GitHub Profile](https://github.com/RudraniSingh100)

## Support
🤝 Want to contribute? Open an issue or a pull request! ✨
