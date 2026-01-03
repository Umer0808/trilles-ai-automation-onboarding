1. Project Overview

In this workflow, we focus on JSON normalization using n8n.
The workflow handles messy CSV-like input with inconsistent headers, automatically detects and maps the headers to a consistent schema (firstName, email), and outputs clean, validated JSON.

This workflow serves as a foundation for future automation projects at Trilles AI, ensuring that all data entering our pipelines is structured and reliable.

2. Workflow Nodes
| Node                                        | Type    | Purpose                                                                                                       |
| ------------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------- |
| Manual Trigger                              | Trigger | Start the workflow manually                                                                                   |
| Code Node: Mock CSV Input                   | Code    | Simulate incoming CSV data with inconsistent headers                                                          |
| Code Node: Normalize Headers + Track Errors | Code    | Map dynamic headers to the strict schema and log any unmapped fields                                          |
| IF Node: Schema Validation Gate             | IF      | Check that `firstName` and `email` are present. Route valid items forward and invalid items to error handling |
| Code Node: Final Output                     | Code    | Produce clean, trimmed, validated JSON ready for downstream use                                               |
| Code Node: Error Formatter                  | Code    | Format errors with detailed information for logging or notifications    |


3. Logic Explanation
Header Normalization

The function normalizeKey() maps header variations like:

F. Name, Name, fname → firstName

Email Address, e-mail → email

Any headers that cannot be mapped are captured in an errors array for review.

Schema Validation

The IF Node checks that both firstName and email exist for each record.

Valid records move to the Final Output node.

Invalid records are sent to the Error Formatter node for processing.

Error Handling

Errors are captured, not thrown, so the workflow does not fail silently.

Each error object includes:

status: FAILED

reason: Schema validation failed

receivedData and detectedIssues

timestamp

This makes the workflow robust, production-safe, and observable.

4. Sample Input

[
  { "F. Name": "Ali", "Email Address": "ali@gmail.com" },
  { "First Name": "Sara", "email": "sara@company.com" },
  { "Name": "Usman", "e-mail": "usman@yahoo.com" }
]

After normalization, the output JSON will have a consistent schema:
[
  { "firstName": "Ali", "email": "ali@gmail.com" },
  { "firstName": "Sara", "email": "sara@company.com" },
  { "firstName": "Usman", "email": "usman@yahoo.com" }
]



