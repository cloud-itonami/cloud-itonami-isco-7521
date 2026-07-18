# Security Policy

This project handles wood treaters operating workflows. Treat vulnerabilities
as potentially high impact even when the demo data is synthetic — this
domain's failure modes include real chemical-exposure and environmental-
handling risk from wood-preservative treatment (including historically toxic
compounds such as creosote and CCA-type formulations), alongside physical
worker-safety risk.

## Do Not Disclose Publicly

Report privately before opening public issues for:

- credential exposure
- real treater, facility or operator data exposure
- authorization bypass
- Wood Treatment Facility Coordination Governor bypass
- audit-ledger tampering
- over-disclosure in reports or exports
- unsafe robot action dispatch
- any path that lets a proposal reach a treatment-execution decision, a
  chemical-safety-clearance decision, or a shop-safety-officer-override
  decision

## Reporting

Use GitHub private vulnerability reporting when available for the repository.
If that is unavailable, contact the repository maintainers through the
cloud-itonami organization before publishing details.

Include:

- affected commit or version
- reproduction steps
- expected and actual behavior
- impact on treater/facility data, policy enforcement or audit logging
- suggested fix, if known

## Production Guidance

- Store secrets outside Git.
- Keep real treater/facility/operator data outside this repository.
- Run policy tests before deployment.
- Export and review audit logs regularly.
- Use least privilege for operators and service accounts.
