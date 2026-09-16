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
