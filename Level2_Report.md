### Level 2 Loan Approval Decision Engine: Explanation and Design Philosophy

This Level 2 Loan Approval Decision Engine extends the Level 1 system by incorporating more complex rule conditions, including multiple variables and logical operators, along with a refined rule priority strategy and robust fallback handling.

#### AND Logic Implementation

To support more nuanced policy definitions, the `match_condition_level2` function has been enhanced to process conditions that include the `AND` logical operator. When a condition string contains `AND`, the function first splits it into individual sub-conditions. Each sub-condition is then evaluated independently. For a rule with an `AND` condition to be considered a match, **all** of its sub-conditions must evaluate to `True`. If even one sub-condition is `False`, the entire `AND` condition fails.

This function also handles chained numeric comparisons, such as `5000 < amount <= 20000`. It intelligently parses these into two separate simple conditions (e.g., `amount > 5000` AND `amount <= 20000`) and evaluates them accordingly. This ensures that complex range-based policies are interpreted accurately and deterministically, without relying on potentially unsafe `eval()` functions.

#### Rule Priority Strategy

In Level 2, multiple rules might potentially match a given user query. To resolve such conflicts and ensure a single, consistent decision, a clear rule priority strategy has been implemented:

1.  **Risk Level Priority**: Rules with a higher `risk_level` take precedence. The system ranks risk levels as `High` > `Medium` > `Low`. For example, if a query matches both a 'Medium' risk rule and a 'High' risk rule, the 'High' risk rule's decision is selected.
2.  **First Match Wins (within same risk level)**: If multiple rules match and share the same `risk_level`, the rule that appears earliest in the `loan_rules_df` (i.e., has a lower original index in the DataFrame) is chosen. This provides a deterministic tie-breaking mechanism.

This strategy is crucial for handling complex scenarios where policies might overlap, ensuring that the most critical or specific policies are applied first.

#### Fallback Behavior

Robust error and fallback handling are essential for production-ready systems. In Level 2, the system includes comprehensive fallback mechanisms:

*   **Missing Variables**: If the `extract_variables_level2` function cannot find a required variable (like `amount` or `credit_score`) in the user's query, that variable will be `None` in the `variables` dictionary. When `_evaluate_single_condition` attempts to use a `None` variable, it will automatically return `False`, preventing that specific condition from matching.
*   **No Rule Matches**: If, after evaluating all rules with the extracted variables, no rule's condition evaluates to `True`, the `loan_decision_engine_level2` returns a `NEED_MORE_INFORMATION` decision. This indicates that the system lacks sufficient, specific rules or information to make a definitive decision.
*   **Ambiguous Input**: For queries that lack clear numerical values or keywords for amount/credit score (e.g., "Just asking about loans."), the variable extraction will result in `None` for both `amount` and `credit_score`, leading to the `NEED_MORE_INFORMATION` fallback.

This ensures that the system always provides a clear status, even when a definitive approval or rejection cannot be made, guiding the user on what further information is required.

#### System Limitations

Despite the enhancements in Level 2, this system still has some limitations:

*   **Fixed Variables**: Only `amount` and `credit_score` are supported. Extending to more variables (e.g., loan term, income) would require modifying the extraction logic and rule parsing.
*   **Limited Operators**: Only basic comparison operators (`<`, `<=`, `>`, `>=`, `==`) and the `AND` logical operator are supported. Complex logical expressions (`OR`, `NOT`, nested conditions) are not yet handled.
*   **Single-Pass Matching**: The system iterates through rules once and applies the first matching rule based on priority. It does not consider multiple, potentially relevant rules that could collectively inform a decision or flag secondary considerations.
*   **No Semantic Understanding**: Variable extraction relies heavily on keyword matching and regex patterns. Subtle semantic variations in user queries that don't align with predefined patterns might lead to missed extractions.
*   **Static Rules**: Rules are loaded from a static CSV. Dynamic rule updates or complex rule management interfaces are not part of this design.

## Summary:

### Data Analysis Key Findings

*   The `loan_rules.csv` file was updated to incorporate five new rules for loan decisions, including conditions with multiple variables (amount, credit\_score) and 'AND' logical operators, as well as chained numeric comparisons (e.g., "5000 < amount <= 20000").
*   A robust `extract_variables_level2` function was developed and refined to accurately extract numeric values for 'amount' and 'credit\_score' from diverse user queries. This function successfully handles various phrasings, special characters (like '$', commas), decimals, and correctly assigns `None` if a variable is not found.
*   The `match_condition_level2` function was implemented to evaluate complex conditions. It correctly parses 'AND' logical operators by evaluating all sub-conditions and accurately handles chained numeric comparisons by breaking them down into simpler, individual comparisons, all while avoiding the use of `eval()`.
*   A rule priority strategy was successfully implemented within the `loan_decision_engine_level2`. This strategy prioritizes rules based on 'risk\_level' (High > Medium > Low) and, for rules with equal risk, selects the first matching rule based on its original order in the DataFrame.
*   The system includes comprehensive fallback mechanisms: if `extract_variables_level2` cannot find a required variable, its value will be `None`; if no rules match a query, the system returns `NEED_MORE_INFORMATION`.
*   After iterative debugging and refinement of the `extract_variables_level2` function, including fixing an `re.error` due to incorrect regex escaping, the system now consistently and accurately extracts both 'amount' and 'credit\_score' from all test cases, including problematic ones involving 'FICO' scores.
*   The `loan_agent_level2` function successfully orchestrates the process, combining the deterministic decision engine with an LLM to provide clear explanations of loan decisions based on the matched rules and conditions.

### Insights or Next Steps

*   **Expand Condition Logic:** Extend the `match_condition_level2` function to support additional logical operators like 'OR' and 'NOT', as well as nested conditions, to allow for more flexible and expressive rule definitions.
*   **Enhance Variable Extraction:** While currently robust for amount and credit score, investigate more advanced natural language processing (NLP) techniques for variable extraction to handle an even wider array of phrasing and implicit references, potentially supporting more diverse types of input.
