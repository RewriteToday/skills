# Reply Routing

Use deterministic routing before autonomous generation.

## Routing Layers

1. System commands (STOP, HELP, START)
2. Security and abuse checks
3. Active workflow handlers (verification, support, notifications)
4. AI-agent fallback
5. Human escalation

## Reply Rules

- Keep replies short and explicit.
- Include stable identifiers for user-facing workflows when useful.
- Avoid sending sensitive data in clear text.
- Respect quiet-hour and opt-out constraints.

## Failure Strategy

- If routing fails, send a safe fallback reply.
- Emit alertable logs for repeated route failures.
- Escalate to human queue on confidence or policy failures.
