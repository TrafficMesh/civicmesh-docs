# Chain of Custody

Evidence handling audit trail:

1. Node captures (GPS-stamped)
2. Backend receives (HMAC verified)
3. Officer reviews (authenticated)
4. Officer approves (digital signature)
5. Citation issued (hash reference)
6. All logged to append-only audit_log

Blake3-signed entries:
- Timestamp, actor, action, result
