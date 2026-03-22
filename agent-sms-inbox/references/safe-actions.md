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

1. Validate the external event or message at the boundary.
2. Validate structured action arguments.
3. Re-check policy with the latest application context.
4. Execute allowlisted actions only.
5. Store the result and policy trace.
6. Return or send a minimal user-facing summary through the outbound SMS flow.

## Deny-By-Default

- Disable free-form code/tool execution.
- Reject unknown actions.
- Reject malformed arguments.
- Reject actions with missing verification context.
- Reject actions that assume Rewrite itself is the source of truth for user permissions.
