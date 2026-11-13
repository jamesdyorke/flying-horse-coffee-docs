# Flying Horse Coffee documentation

Shared product plans, integration decisions and working documentation.
Implementation-specific build, release and module guidance stays with its code.

This repository is public. Never commit customer records, credentials, private
access notes or raw operational evidence. Review every migration before publishing.

Only James may merge pull requests or enable auto-merge.

## Shared subscription documents

- [SUBSCRIPTION_INTEGRATION_AUDIT.md](docs/SUBSCRIPTION_INTEGRATION_AUDIT.md)
- [SUBSCRIPTION_IMPLEMENTATION.md](docs/SUBSCRIPTION_IMPLEMENTATION.md)
- [SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md](docs/SHOPIFY_SUBSCRIPTION_STORE_FINDINGS.md)
- [Introductory subscription offer and rollout plan](docs/INTRODUCTORY_SUBSCRIPTION_OFFER.md)

## Loyalty and rewards

- [Flying Horse Rewards integration and migration plan](docs/LOYALTY_INTEGRATION_PLAN.md)
- [Vendor enquiries and provider proof checklist](docs/LOYALTY_PROVIDER_DISCOVERY.md)

These are the canonical shared Rewards documents. The backend repository keeps
its [API and operational contract](https://github.com/jamesdyorke/flying-horse-coffee-firebase-backend/blob/develop/docs/REWARDS_BACKEND.md)
with the implementation; it does not own a separate product plan.

## Validation

The backend repository owns trusted docs validation. Its `Shared documentation`
workflow uses the existing `backend-local-ci` queue and `fhc-backend` runner.
After its rollout, it checks develop and trusted-collaborator-authored, same-repository open PRs
every 10 minutes, and can also be dispatched manually. Results and exact checked
commit hashes appear in backend Actions summaries, not as docs PR check badges.
Fork contributions must be reviewed and imported by James before local execution.

Internal staging/customer handoff logs remain in their private owning repositories;
they are not suitable for copying into this public repository.
