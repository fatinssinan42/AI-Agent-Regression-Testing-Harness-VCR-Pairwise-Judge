 AI Agent Regression Testing Harness: VCR & Pairwise Judge

One recorded run, two agents, one verdict — the testing rig that shows you what changed in your AI agent before your users do.

This repository contains a complete, CI-ready framework for evaluating autonomous AI agents. It catches schema slips, infinite tool-calling loops, and behavioral regressions by running a new build side-by-side with a production baseline and scoring the differences via deterministic checks and an LLM-as-a-Judge.

Core Methodology

An agent's quality lives in its behavior, and behavior only shows itself when two versions stand next to each other. This rig enforces a strict three-step pipeline:

1. Record Once: One live run is saved as a JSON fixture containing every step, every tool call, and every result returned.
2. Replay for Free (VCR Proxy): The fixture answers the same API and tool calls, allowing the suite to run in seconds with zero external API costs.
3. Judge in Pairs: Both traces (Baseline and Candidate) are routed to a pairwise evaluator that returns a winner, a score delta, and the exact regressions spotted.

 Architecture Map

The CI runner points both builds at the same proxy, and the proxy serves the recorded fixtures back. The only variable between the two runs is the agent itself.


                  +-----------------------------------+
                  |        CI/CD Runner / Harness     |
                  +-----------------------------------+
                                    |
                    +---------------+---------------+
                    |                               |
                    v                               v
         +--------------------+           +--------------------+
         |   Baseline Agent   |           |  Candidate Agent   |
         |  (v1.0 Production) |           |  (v1.1 PR / Test)  |
         +--------------------+           +--------------------+
                    |                               |
                    +---------------+---------------+
                                    |
                                    v
                  +-----------------------------------+
                  |     VCR Proxy & Trace Collector   |
                  |  - Replays API/Tool Mock Data     |
                  |  - Serializes Steps & Tool Args   |
                  +-----------------------------------+
                                    |
                    +---------------+---------------+
                    |                               |
                    v                               v

    Layer 1: Deterministic Evals       Layer 2: Pairwise Judge      
    - JSON Schema Validation          - Trajectory Quality Comparison 
    - Step Count / Loop Bounds        - Instruction Adherence         
    - State Mutation Diffs            - LLM-as-a-Judge Rubrics       
                  
                     CI Gatekeeper & Quality Report  
                 
