
# Project Results

This document summarizes the deliverables, experiments, and outcomes of the Loan Approval Decision Agent project.

---

# Deliverables

The project includes the following components:

1. Loan Rules CSV
2. Deterministic Decision Engine
3. Variable Extraction System
4. Safe Condition Evaluation Engine
5. Hybrid LLM Explanation Layer
6. Test Case Evaluation
7. Level 1 and Level 2 implementations

---

# Level 1 Results

Level 1 implements a simple rule engine using only the loan amount.

### Tested Scenarios

| Case | Input | Decision | Risk |
|----|----|----|----|
| 1 | I need 2000 | APPROVED | Low |
| 2 | Loan request 4000 | APPROVED | Low |
| 3 | Can I borrow 5000 | APPROVED | Low |
| 4 | I want 15000 loan | MANUAL_REVIEW | Medium |
| 5 | Need 8000 | MANUAL_REVIEW | Medium |
| 6 | Loan for 19999 | MANUAL_REVIEW | Medium |
| 7 | Give me 50000 | REJECTED | High |
| 8 | I need 21000 | REJECTED | High |
| 9 | Apply for 100000 | REJECTED | High |

### Observations

- Regex extraction correctly identifies loan amounts.
- Rule engine consistently selects the correct rule.
- LLM explanations remain consistent with deterministic decisions.

---

# Level 2 Results

Level 2 introduces additional complexity:

- multiple variables
- AND logic
- rule prioritization

### Variables Extracted

- amount
- credit_score

### Sample Outcomes

| Input | Decision | Risk |
|----|----|----|
| Loan 3000 score 800 | APPROVED | Low |
| Need 15000 score 700 | MANUAL_REVIEW | Medium |
| Loan 25000 score 720 | MANUAL_REVIEW | Medium |
| Loan 25000 score 650 | REJECTED | High |
| Credit score 550 | REJECTED | High |
