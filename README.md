
# Loan Approval Decision Agent
Hybrid Rule-Based + LLM Explanation System

## Project Overview
This project implements a **Loan Approval Decision Agent** using a hybrid architecture that combines:

- Deterministic rule-based decision logic
- Large Language Model (LLM) explanation layer

The system ensures that **loan decisions are always made by deterministic Python logic**, while the LLM is used only to generate human-readable explanations.

This architecture prevents hallucination and guarantees consistent financial decision-making.

---

# Architecture

User Query  
↓  
Variable Extraction  
↓  
Rule Matching Engine  
↓  
Decision Selection  
↓  
LLM Explanation  
↓  
Structured Output

---

# Level 1 – Basic Hybrid Decision Engine

Level 1 implements a simple rule-based decision system.

### Supported Rules

Rules are stored in a CSV file with the following fields:

- section
- rule_description
- condition
- decision
- risk_level

Example:

| section | rule_description | condition | decision | risk_level |
|-------|--------|--------|--------|--------|
| Loan | Small loan auto approved | amount <= 5000 | APPROVED | Low |
| Loan | Medium loan needs review | amount <= 20000 | MANUAL_REVIEW | Medium |
| Loan | High loan rejected | amount > 20000 | REJECTED | High |

### Level 1 Workflow

1. Extract loan amount using regex
2. Evaluate rule conditions safely
3. Select the first matching rule
4. Send decision to LLM for explanation

### Key Design Principle

The **LLM never makes the decision**.

Only the rule engine decides.

---

# Level 2 – Advanced Decision Engine

Level 2 extends the system with:

- Multi-variable extraction
- AND logical conditions
- Chained numeric comparisons
- Rule priority strategy

### Supported Variables

- amount
- credit_score

### Example Rule

amount > 20000 AND credit_score < 700

### Rule Priority Strategy

Rules are prioritized by risk level:

High > Medium > Low

If risk levels are equal, the **first rule wins**.

---

# Technologies Used

- Python
- Pandas
- Regex
- HuggingFace Transformers
- Phi-3.5 Mini Instruct Model
- BitsAndBytes Quantization

---

# Running the Project

Install dependencies:

pip install transformers accelerate bitsandbytes pandas

---

