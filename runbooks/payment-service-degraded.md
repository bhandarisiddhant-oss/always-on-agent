# Runbook: Payment Service Degraded

**Symptoms:**
- p99 latency on `/checkout` > 2s
- Error rate on `PaymentService.processCharge` elevated
- NullPointerException or TimeoutException in stack traces

**First checks (in order):**

1. **Recent deploys.** Check `deploys/recent.json` for any deploy to `payment-service` in the last 60 minutes. The single most common cause is a deploy regression.

2. **Stripe upstream.** Check status.stripe.com. If Stripe is degraded, our service will surface as broken.

3. **Database connection pool.** Check `payment-service` pod metrics for connection pool exhaustion. A surge in concurrent checkouts can exhaust the pool. The fix is to bump `db.pool.max` from default (20) to 50.

4. **The known NPE bug.** If the stack trace shows `PaymentService.java:142`, this is the unfixed bug where `customer.savedPaymentMethod` is null for customers in the new "guest checkout" flow. The hotfix is a null check at line 142, falling back to the request's `paymentMethodToken`.

**Severity guide:**
- All customers affected → P0, page #payments-oncall immediately
- One tenant affected → P1, page CSM and on-call
- Single user affected → P3, ticket only

**Recent incidents matching this pattern:**
- PROD-4012 (March 2026): caused by deploy regression, rolled back
- PROD-3891 (February 2026): caused by Stripe outage
- PROD-3754 (December 2025): caused by connection pool exhaustion during Black Friday
