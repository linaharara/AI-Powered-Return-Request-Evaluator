# 🤖 AI-Powered Return Request Evaluator (Agent-Style Decision System)

## 📌 Overview
A no-code automation workflow that automatically evaluates customer return requests against company policy using **Claude (Anthropic)**, then routes each request into one of three outcomes: auto-approve, auto-reject, or escalate for human review. This is the capstone project of the portfolio, combining prompt engineering, RAG-lite policy grounding, structured JSON output, and conditional routing into a single agent-style decision pipeline.

## 🎯 Problem Solved
Manually reviewing every return request — checking dates, item condition, and policy exceptions — is repetitive and slow, yet fully automating it risks approving requests that don't clearly fit the rules. This project solves that by:
- Automatically approving/rejecting requests that clearly match or violate policy
- Automatically flagging ambiguous or exception cases for human judgment, instead of guessing
- Providing a clear, policy-referenced reason for every decision
- Reducing manual review workload to only the cases that genuinely need it

## 🛠 Tools Used
- **n8n** – Workflow automation platform
- **Claude (Anthropic API)** – Generative AI model (claude-sonnet-5)
- **Google Sheets** – Data source (return requests)
- **n8n IF Node** – Conditional routing logic

## ⚙ Workflow
```
[Manual Trigger] 
      ↓
[Read return requests from Google Sheets] 
      ↓
[Send request + policy document to Claude, requesting a structured decision]
      ↓
[IF Node checks whether the decision is "Needs Human Review"]
      ↓
   ┌─────────────┴─────────────┐
   ↓ (True)                    ↓ (False)
Flagged for human review    Auto-approved / auto-rejected
```

## 🧠 Prompt Design — Combining All Prior Techniques
This project deliberately combines every prompting technique used across the portfolio:
- **Policy grounding (RAG-lite)**: the full return policy is injected into the prompt, and the model is instructed to decide based ONLY on it — preventing invented exceptions
- **Structured JSON output**: the response is a machine-readable object (`decision` + `reason`), enabling the automation to route on it
- **Explicit decision rules**: clear criteria define when to use `Auto-Approve`, `Auto-Reject`, or `Needs Human Review`, rather than leaving judgment entirely to the model
- **One-sentence reasoning requirement**: every decision must cite the specific policy rule applied, making the system's logic auditable

## 📊 Sample Output & Validation
Tested on 5 return requests covering distinct scenarios:
| Request | Decision | Reasoning |
|---|---|---|
| Unused item, within 14 days, not on sale | **Auto-Approve** | All policy conditions met |
| Sale item | **Auto-Reject** | Sale items are final sale per policy |
| Unused item, 21 days after delivery | **Auto-Reject** | Exceeds the 14-day return window |
| Defective item, but used once before reporting | **Needs Human Review** | Policy covers defects, but doesn't address prior use — a genuine edge case |
| Unused item, within 14 days, size exchange | **Auto-Approve** | All policy conditions met |

The IF Node correctly split the 5 requests into a 1-item "human review" branch and a 4-item "automated decision" branch — exactly matching expected outcomes, including the one deliberately ambiguous case.

## 💡 Key Takeaways
- A trustworthy automated decision system needs a **third option** — not just approve/reject, but also "escalate to a human" — for cases the rules don't clearly cover
- Combining RAG-lite grounding + structured output + conditional logic turns a simple chatbot pattern into something closer to a true decision-making AI agent
- Requiring the model to justify each decision with one policy-referenced sentence makes the system auditable — critical for any real business deploying AI to make customer-facing decisions
- This project demonstrates the full arc of the portfolio: from basic prompt templates (Project 1) to a multi-technique, agent-style automation (Project 5)

## 🚀 Future Improvements
- Automatically issue refunds via a payments API for "Auto-Approve" cases
- Send a notification (Slack/email) to the support team for every "Needs Human Review" case
- Log all decisions with timestamps to build an auditable return-decision history
