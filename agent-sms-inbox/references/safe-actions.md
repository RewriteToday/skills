# Safe Action Design

## Define Actions As Contracts

Each action must define:

- name
- allowed roles/contexts
- strict input schema
- max execution time
- failure mode
- audit payload

## Execution Pattern

1. Validate structured arguments.
2. Re-check policy with current context.
3. Execute allowlisted action only.
4. Store result and policy trace.
5. Return minimal user-facing summary.

## Deny-By-Default

- Disable free-form code/tool execution.
- Reject unknown actions.
- Reject malformed arguments.
- Reject actions with missing verification context.
