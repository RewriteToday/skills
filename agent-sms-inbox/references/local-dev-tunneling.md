# Local Development And Tunneling

Use secure tunneling for inbound webhook testing.

## Steps

1. Run local API server with webhook endpoint.
2. Expose endpoint through a tunneling tool.
3. Register temporary webhook URL in Rewrite environment.
4. Send test inbound SMS events.
5. Validate signature verification and dedupe behavior.

## Local Safety Checks

- Reject invalid signatures.
- Reject stale timestamps.
- Process each event once.
- Verify policy matrix with synthetic risky prompts.
