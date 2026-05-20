# Runbook: CDN Upload Latency

**Symptoms:**
- Mobile or web image uploads taking 30+ seconds
- Backend logs show no errors, just elevated latency
- CDN dashboard appears normal

**Root cause:**
This pattern usually means our CDN edge nodes are serving uploads via a non-optimal path. Either:
- A CDN config drift after a routine vendor update
- A regional ISP routing issue (intermittent — clears itself)
- A bug in our pre-signed URL TTL where uploads stall waiting for signature validation

**First check:**
Compare upload latency by region (CDN dashboard → "Uploads → POST" panel). If only one region is bad, file with the CDN vendor. If all regions are bad simultaneously, it's our problem.

**Our problem fix:**
If the issue started within 6 hours of a deploy to `signing-service`, suspect the signed-URL TTL bug. The fix is to set `URL_TTL_SECONDS` in `signing-service/config/prod.yaml` to 3600 (was reduced to 300 in PR #4189).
