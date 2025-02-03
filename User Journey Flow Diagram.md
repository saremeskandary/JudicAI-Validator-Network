## JudicAI-Validator-Network User Journey

### **1. User Journey Flow Diagram (Mermaid Graph TD)**

```mermaid
graph TD
    A[Start: AI Agent Registration]
    B[Identity Verification via Coinbase CDP]
    C[Stake ETH on EigenLayer]
    D[Issue NFT Proof on Flow]
    E[Registration Complete]
    
    F[Service Provision Initiated]
    G[Service Delivery by AI Provider]
    H[Evidence & Documentation Provided]
    
    I[Validator AI Agents Review Service]
    J[Initiate Decentralized Consensus]
    K{Consensus Reached?}
    L[Record Outcome on Arbitrum]
    M[Distribute Rewards]
    N[Apply Penalties/Slash Stake]
    O[Escalate to DAO Fallback]
    P[DAO Voting & Resolution]
    Q[Final Outcome Recorded On-chain]

    R[User Raises Objection]
    S[Pay Gas Cost for Objection]
    T[Re-evaluate Service with Objection]
    U{Objection Valid?}
    V[Adjust Outcome Based on Objection]
    
    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> I
    I --> J
    J --> K
    K -- Yes --> L
    L --> M
    L --> N
    K -- No --> O
    O --> P
    P --> Q
    
    %% New Steps for Objections
    Q --> R
    R --> S
    S --> T
    T --> U
    U -- Yes --> V
    V --> Q
    U -- No --> Q
```

### **2. Service Validation Sequence Diagram (Mermaid SequenceDiagram)**

```mermaid
sequenceDiagram
    participant User as Service Consumer/User
    participant Provider as AI Agent Provider
    participant Validator as Validator AI Agents
    participant Eigen as EigenLayer (Registration & Staking)
    participant Arbitrum as Arbitrum (Smart Contracts)
    participant DAO as DAO (Dispute Resolution)
    participant Coinbase as Coinbase CDP
    participant Flow as Flow (NFT Issuance)

    %% Registration & Onboarding
    Provider->>Coinbase: Verify Identity
    Coinbase-->>Provider: Identity Verified
    Provider->>Eigen: Register & Stake ETH
    Eigen-->>Provider: Registration Confirmation
    Eigen->>Flow: Mint NFT Proof of Registration
    Flow-->>Provider: NFT Issued
    
    %% Service Provision
    Provider->>Arbitrum: Submit Service & Evidence
    Arbitrum-->>Validator: Notify Service for Validation
    
    %% Validation Process
    Validator->>Arbitrum: Retrieve Service Data
    Validator->>Validator: Evaluate Conditions
    Validator->>Validator: Initiate Consensus Process
    
    %% Consensus Outcome
    alt Consensus Reached
        Validator->>Arbitrum: Record Consensus Outcome
        Arbitrum-->>Provider: Distribute Rewards / Apply Penalties
        
        %% User Objection Handling
        User->>Arbitrum: Raise Objection
        Arbitrum-->>User: Confirm Gas Cost Payment
        User->>Arbitrum: Pay Gas Cost
        Arbitrum-->>Validator: Re-evaluate Service with Objection
        Validator->>Arbitrum: Validate Objection
        alt Objection Valid
            Validator->>Arbitrum: Adjust Outcome Based on Objection
            Arbitrum-->>Provider: Update Final Outcome (Reward or Penalty)
        else Objection Invalid
            Arbitrum-->>User: Retain Gas Cost (No Refund)
        end
    else Consensus Fails
        Validator->>DAO: Escalate Dispute to DAO
        DAO->>DAO: Token Holder Voting & Resolution
        DAO->>Arbitrum: Submit Final Decision
        Arbitrum-->>Provider: Execute Final Outcome (Reward or Penalty)
    end
