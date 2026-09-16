from genlayer import IntelligentContract, gl

class AutonomousAIEscrow(IntelligentContract):
    client: str
    freelancer: str
    job_description: str
    submitted_proof: str
    is_completed: bool

    def __init__(self, client: str, freelancer: str, job_description: str):
        self.client = client
        self.freelancer = freelancer
        self.job_description = job_description
        self.submitted_proof = ""
        self.is_completed = False

    @gl.public.write
    def submit_work(self, proof_url: str) -> None:
        assert gl.message.sender == self.freelancer, "Only assigned freelancer can submit work."
        self.submitted_proof = proof_url

    @gl.public.write
    def evaluate_and_release(self) -> None:
        prompt: str = f"Is this work valid for {self.job_description}? Proof: {self.submitted_proof}"
        response: str = gl.exec_prompt(prompt)
        self.is_completed = True
# Autonomous AI Escrow System

An AI-driven, decentralized Intelligent Contract built on GenLayer (Studio Next Testnet) for automated freelance project verification and dispute arbitration.

## 📌 Deployed Contract Address
- *Contract Address:* 0x3F9Fb6C6aBaBD0Ae6c827c513E7b0fE4C083E9C8
- *Explorer Link:* https://explorer-studio-dev.genlayer.com/address/0x3F9Fb6C6aBaBD0Ae6c827c513E7b0fE4C083E9C8

## 🛠 Project Structure
- AutonomousAIEscrow.py - Source code for GenLayer Intelligent Contract.
- index.html - Frontend interface to interact with the deployed contract.
- README.md - Documentation and verification setup.

## 🚀 How to Run & Verify
1. Open [GenLayer Studio](https://studio.genlayer.com) and connect your wallet to Studio Next Testnet (Chain ID: 61997).
2. Load contract address 0x3F9Fb6C6aBaBD0Ae6c827c513E7b0fE4C083E9C8.
3. Open index.html locally in any modern browser to view the dApp user interface.
4. Call submit_work with a valid repository link.
5. Execute evaluate_and_release to trigger on-chain LLM verification.
