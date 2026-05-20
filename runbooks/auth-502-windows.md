# Runbook: Login 502 Windows

**Symptoms:**
- Bursts of HTTP 502 on `POST /auth/login` lasting 20-60 seconds
- No related deploys
- Upstream timeouts in load balancer logs

**Root cause (95% of the time):**
Connection pool exhaustion on the `auth-service` → `users-db` link. The auth service holds connections for the duration of the bcrypt hash check, which is intentionally slow. Under load spikes, the pool drains faster than it refills.

**The fix:**
1. Confirm: ssh to an auth-service pod and run `curl localhost:9090/metrics | grep db_pool`. If `db_pool_idle` is 0 during the 502 windows, that's the cause.
2. Short-term: bump `DB_POOL_SIZE` in `auth-service/config/prod.yaml` from 30 to 60 and redeploy.
3. Long-term: switch to argon2id (faster than bcrypt at the same security level) — tracked in PROD-4221.

**Don't:**
- Don't scale up auth-service pods. More pods = more pool drain on the same database.
- Don't increase load balancer timeout. That hides the symptom, doesn't fix the cause.
