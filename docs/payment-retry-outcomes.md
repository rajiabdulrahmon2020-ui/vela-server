# Retryable payment outcomes

Payment responses include `retryable` so clients can distinguish a recoverable
provider outcome from a terminal payment result without parsing error text.

| Status | `retryable` | Client action |
| --- | --- | --- |
| `CREATED` | `false` | Authorize the existing intent |
| `AUTHORIZED` | `false` | Submit the existing signed intent |
| `SUBMITTED` | `true` | Poll or retry submission with the same payment ID; the server reconciles the stored hash |
| `CONFIRMED` | `false` | Display the existing settlement |
| `FAILED` | `false` | Show the failure and create a fresh intent only from a fresh request |

Provider timeouts and transient polling failures leave a payment in
`SUBMITTED`, which is the only retryable response state. A failed transaction,
invalid signed envelope, expired intent, or rejected authorization is terminal
for that payment. Retrying must never create a second Stellar transaction.

The flag describes server-side payment state, not a guarantee that a live
provider is available. Clients should still use bounded backoff and should
never log signed XDR, keys, access tokens, or raw provider responses. Local and
CI examples are testnet-only.
