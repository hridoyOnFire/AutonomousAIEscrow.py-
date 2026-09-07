from genlayer import IntelligentContract, gl

class AutonomousAIEscrow(IntelligentContract):
    def __init__(self, client: str, freelancer: str, job_description: str):
        """
        Initializes the Escrow Contract with client, freelancer, and work parameters.
        """
        self.client = client
        self.freelancer = freelancer
        self.job_description = job_description
        self.submitted_proof = ""
        self.is_completed = False
        self.is_refunded = False
        self.ai_review_feedback = ""

    @gl.public
    def submit_work(self, proof_url: str):
        """
        Freelancer submits the URL or proof of completed work.
        """
        assert gl.message.sender == self.freelancer, "Only assigned freelancer can submit work."
        assert not self.is_completed, "Work is already completed and finalized."
        
        self.submitted_proof = proof_url

    @gl.public
    def evaluate_and_release(self):
        """
        Triggers AI LLM consensus to inspect the submitted work against the job requirements.
        If approved, funds are automatically released to the freelancer.
        """
        assert not self.is_completed, "Contract already finalized."
        assert len(self.submitted_proof) > 0, "No work proof submitted yet."

        # Constructing AI Validation Prompt for GenLayer consensus
        prompt = f"""
        Act as an impartial AI Code & Content Reviewer.
        
        Job Requirements:
        {self.job_description}
        
        Submitted Work Link/Proof:
        {self.submitted_proof}
        
        Task: Analyze the proof against requirements. 
        Respond strictly in this JSON-like structure without extra formatting:
        VERDICT: [PASSED or FAILED]
        REASON: [Short explanation in 1-2 sentences]
        """

        # AI Execution via GenLayer Engine
        ai_response = gl.exec_prompt(prompt)
        self.ai_review_feedback = ai_response

        # Logic based on AI Decision
        if "VERDICT: PASSED" in ai_response:
            self.is_completed = True
            contract_balance = gl.get_balance(self.address)
            if contract_balance > 0:
                gl.transfer(self.freelancer, contract_balance)
        elif "VERDICT: FAILED" in ai_response:
            self.is_refunded = True
            contract_balance = gl.get_balance(self.address)
            if contract_balance > 0:
                gl.transfer(self.client, contract_balance)
        else:
            raise Exception("AI Evaluation was inconclusive. Please re-trigger.")

