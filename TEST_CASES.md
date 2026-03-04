
# Test Cases

This document includes test cases for both Level 1 and Level 2 systems.

---

# Level 1 Test Cases

Level 1 uses only the **loan amount**.

## Approved Cases

| Input | Expected Decision | Risk |
|----|----|----|
| I need 2000 | APPROVED | Low |
| Loan for 4000 | APPROVED | Low |
| Can I borrow 5000 | APPROVED | Low |

## Manual Review Cases

| Input | Expected Decision | Risk |
|----|----|----|
| I want 15000 loan | MANUAL_REVIEW | Medium |
| Need 6000 dollars | MANUAL_REVIEW | Medium |
| Loan for 19999 | MANUAL_REVIEW | Medium |

## Rejected Cases

| Input | Expected Decision | Risk |
|----|----|----|
| Give me 50000 | REJECTED | High |
| Loan request 21000 | REJECTED | High |
| Apply for 100000 | REJECTED | High |

---

# Level 2 Test Cases

Level 2 evaluates:

- loan amount
- credit score
- AND conditions

## Low Risk

| Input | Decision |
|----|----|
| Loan 3000 credit score 800 | APPROVED |
| Need 2500 score 750 | APPROVED |
| Borrow 5000 score 720 | APPROVED |

## Medium Risk

| Input | Decision |
|----|----|
| Loan 15000 score 700 | MANUAL_REVIEW |
| Need 6000 credit 650 | MANUAL_REVIEW |
| Loan 25000 score 720 | MANUAL_REVIEW |

## High Risk

| Input | Decision |
|----|----|
| Loan 25000 score 650 | REJECTED |
| Loan 30000 credit 620 | REJECTED |
| Credit score 550 | REJECTED |

## Missing Information

| Input | Decision |
|----|----|
| I want a loan | NEED_MORE_INFORMATION |
| Tell me about loans | NEED_MORE_INFORMATION |
