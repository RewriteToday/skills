# Local Development, Forwarding, And Replay

Prefer official Rewrite tooling for local event intake when possible.

## Preferred Local Flow

1. Run your local API server with the Rewrite webhook endpoint.
2. Authenticate the CLI with `rewrite login`.
3. Forward live Rewrite events into your local server:

```bash
rewrite listen --forward-to http://localhost:3000/webhooks/rewrite
```

4. Validate signature verification, dedupe, and policy gating locally.
5. Use delivery logs to debug failures or missing events.

## When To Use A Tunnel

- Use a tunneling tool only when you need Rewrite to call a concrete registered local URL directly.
- Public webhook endpoints must be HTTPS in production.
- `http://localhost/...` is accepted for local development.

## Local Safety Checks

- Reject invalid signatures.
- Process each webhook event once.
- Replay stored payloads against non-production workers only.
- Verify the policy matrix with synthetic risky prompts before allowing outbound replies.
