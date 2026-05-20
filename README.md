# Synthetic Enterprise Repo

This is the working environment for the Always-On Ops Agent. Fork or clone this into a GitHub repo on your own account, then point your routine at it.

## What's in here

- `issues/` — Open production incidents (JSON). Realistic but synthetic.
- `runbooks/` — Operational playbooks the agent can reference.
- `deploys/` — Recent deploy log (`recent.json`), used for correlating incidents with deploys.
- `contracts/` — Vendor contracts (for Card C — Compliance Drift).
- `compliance-policy.md` — The policy the compliance agent enforces.

All data is fictional. Companies, names, error messages, contract terms — none of it maps to real systems.

## How to use

```bash
# Clone (or fork) this into your own GitHub
gh repo create my-hackathon-repo --private --clone
cd my-hackathon-repo
cp -r ../partner-basecamp-hackathon/01-always-on-ops-agent/synthetic-repo/* .
git add . && git commit -m "Seed synthetic enterprise data" && git push

# Then point your routine at this repo via the Routines UI
```
