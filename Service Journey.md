### **Service Journey**

#### **1. Initiating a Service Validation Request**
**User Action:**  
The user logs into the dashboard, selects "Initiate Validation Request," provides context (e.g., case description, evidence), and specifies the type of validator needed (e.g., lawyer, judge).

**Background Operations:**
1. **Frontend Interaction:**  
   - The frontend sends an API request to the backend with the validation request details.
2. **Smart Contract Function Call:**  
   - `ValidationLogic.sol` → `submitValidationRequest(uint256 requestId, string memory context, string memory evidence)`  
     - Logs the request in the contract.
     - Emits an event (`ValidationRequested`) to notify validators.
3. **Validator Assignment:**  
   - A service or offchain component queries the list of available AI agents based on their roles and assigns the request to eligible validators.
4. **Role Verification via Flow NFTs:**  
   - Validators must hold an NFT issued by the Flow blockchain during registration, proving their role (e.g., lawyer, judge). This ensures only qualified agents can participate in evaluations.

**Services Used:**
- Frontend Dashboard
- Backend API
- Smart Contract (`ValidationLogic.sol`)
- **Flow Blockchain** (for NFT-based role verification)

---

#### **2. Validators Evaluate the Request**
**User Action:**  
Validators (AI agents) review the request and submit their judgments.

**Background Operations:**
1. **Notification Mechanism:**  
   - Validators receive notifications via events emitted by the smart contract (`ValidationRequested`).
2. **Stake Verification via EigenLayer:**  
   - Before submitting a judgment, the system verifies that the validator has sufficient stake locked in EigenLayer. This ensures accountability and reduces malicious behavior.
3. **Evaluation Process:**  
   - Validators evaluate the request using their predefined logic.
4. **Smart Contract Function Call:**  
   - `ValidationLogic.sol` → `submitJudgment(uint256 requestId, bool isValid, string memory reasoning)`  
     - Records the judgment and reasoning provided by each validator.
     - Updates the state of the request.

**Services Used:**
- Validator AI Agents (configured using Autonome.fun)
- Smart Contract (`ValidationLogic.sol`)
- **EigenLayer** (for stake verification)

---

#### **3. Reaching Consensus**
**User Action:**  
The system determines whether consensus has been reached among validators.

**Background Operations:**
1. **Consensus Check:**  
   - `ValidationLogic.sol` → `checkConsensus(uint256 requestId)`  
     - Evaluates all submitted judgments for the request.
     - Determines if a majority decision exists.
2. **Outcome Recording:**  
   - If consensus is reached:
     - `ValidationLogic.sol` → `recordOutcome(uint256 requestId, bool finalDecision)`  
       - Records the final outcome on-chain.
       - Emits an event (`ConsensusReached`) to notify the user.
   - If no consensus is reached:
     - Escalates the request to the DAO fallback mechanism.

**Services Used:**
- Smart Contract (`ValidationLogic.sol`)
- DAO Fallback Mechanism (Collab.Land)

---

#### **4. Raising an Objection**
**User Action:**  
If the user disagrees with the decision, they click "Raise Objection," provide details, and pay the gas cost.

**Background Operations:**
1. **Gas Cost Calculation:**  
   - `GasCostLib.sol` → `calculateGasCost(string memory objectionDescription)`  
     - Calculates the gas cost based on the complexity of the objection.
2. **Objection Submission:**  
   - `ValidationLogic.sol` → `raiseObjection(uint256 requestId, string memory description)`  
     - Logs the objection and deducts the gas cost from the user's wallet.
     - Emits an event (`ObjectionRaised`) to notify higher-level validators.
3. **Re-evaluation:**  
   - Higher-level validators review the objection and either uphold or dismiss it.
   - `ValidationLogic.sol` → `resolveObjection(uint256 requestId, bool isValid)`  
     - Adjusts the outcome based on the validity of the objection.
4. **Stake Adjustment via EigenLayer:**  
   - If the objection is valid, rewards are distributed to the user or the objecting party. If invalid, the gas cost is retained, and penalties may be applied to the objector’s stake in EigenLayer.

**Services Used:**
- Smart Contract (`ValidationLogic.sol`, `GasCostLib.sol`)
- Validator AI Agents
- **EigenLayer** (for stake adjustment)

---

#### **5. DAO Fallback for Dispute Resolution**
**User Action:**  
If the dispute cannot be resolved by validators, the system triggers the DAO fallback.

**Background Operations:**
1. **Escalation Trigger:**  
   - `DAOFallback.sol` → `escalateDispute(uint256 requestId, string memory description)`  
     - Logs the dispute and notifies token holders via Collab.Land.
2. **Identity Verification via Coinbase CDP:**  
   - Token holders participating in the vote must first verify their identity through Coinbase CDP to ensure legitimacy and prevent Sybil attacks.
3. **Voting Process:**  
   - Token holders connect their wallets and cast votes using the DAO interface.
   - Votes are tallied on-chain using smart contracts.
4. **Enforcement of Decision:**  
   - `DAOFallback.sol` → `resolveDispute(uint256 disputeId, bool outcome)`  
     - Enforces the majority decision and updates the outcome on-chain.

**Services Used:**
- Smart Contract (`DAOFallback.sol`)
- **Coinbase Developer Platform (CDP)** (for identity verification)
- Collab.Land (for token-gated voting)

---

#### **6. Reward and Penalty Distribution**
**User Action:**  
Rewards are distributed to validators for correct decisions, and penalties are enforced for incorrect or malicious behavior.

**Background Operations:**
1. **Reward Distribution:**  
   - `RewardPenalty.sol` → `distributeReward(address validator, uint256 amount)`  
     - Transfers rewards to validators.
2. **Penalty Enforcement:**  
   - `RewardPenalty.sol` → `enforcePenalty(address validator, uint256 amount)`  
     - Calls the staking contract to slash the validator's stake.
3. **Staking Mechanism via EigenLayer:**  
   - `Staking.sol` → `slash(address agent, uint256 amount)`  
     - Deducts the penalty amount from the validator's stake in EigenLayer.

**Services Used:**
- Smart Contracts (`RewardPenalty.sol`, `Staking.sol`)
- **EigenLayer** (for staking and slashing)

---

### **Summary of Key Services Used**

| **Step**                  | **Service/Platform**        | **Purpose**                                                                 |
|---------------------------|----------------------------|-----------------------------------------------------------------------------|
| Role Verification         | **Flow Blockchain**         | Ensures validators hold NFTs proving their roles (e.g., lawyer, judge).    |
| Stake Verification        | **EigenLayer**             | Verifies validators' stakes before allowing participation in evaluations.   |
| Identity Verification     | **Coinbase Developer Platform (CDP)** | Ensures legitimate participation in DAO voting through identity checks.    |
| Gas Cost & Objections     | **EigenLayer**             | Handles stake adjustments for valid/invalid objections.                     |
| DAO Voting                | **Collab.Land**            | Facilitates token-gated community engagement and dispute resolution voting.|
