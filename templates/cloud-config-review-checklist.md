# Recurring Cloud Configuration Review Checklist

Run this at the cadence defined in your risk management policy (quarterly recommended at minimum). Complete one copy per cloud account/subscription/project.

## Identity and Access Management

- [ ] No IAM users/service principals with unused permissions in the last 90 days.
- [ ] No long-lived access keys older than the organization's rotation policy.
- [ ] MFA enforced on all human identities with console/API access.
- [ ] No wildcard (`*:*`) permissions on any role outside a documented break-glass account.
- [ ] Break-glass/emergency-access accounts are monitored and their use is alerted on.
- [ ] Root/global administrator account has MFA enabled and is not used for routine work.

## Logging and Monitoring

- [ ] Account/organization-wide audit logging (CloudTrail / Azure Activity Log / GCP Audit Logs) is enabled and cannot be disabled by a non-privileged role.
- [ ] Logs are shipped to a centralized, access-restricted log store outside the account being logged.
- [ ] Alerting is configured for high-risk events (root login, IAM policy changes, security group/firewall changes, disabling of logging itself).

## Network

- [ ] No storage buckets/containers with public read/write access unless explicitly documented and approved.
- [ ] No databases or management interfaces directly exposed to the public internet.
- [ ] Default security groups/network security groups deny all inbound traffic by default.
- [ ] VPC/VNet flow logs enabled for production environments.

## Data Protection

- [ ] Encryption at rest enabled for all storage, database, and backup resources.
- [ ] Encryption in transit (TLS) enforced for all data-plane endpoints.
- [ ] Key rotation policy in place and being followed for customer-managed keys.
- [ ] Backups tested for restorability within the last review cycle.

## Compliance Baseline

- [ ] Latest run of the relevant CIS Benchmark / CSPM scan (Prowler/ScoutSuite/native tool) reviewed, with findings triaged into the risk register.
- [ ] No findings open past their remediation due date without a documented risk acceptance.

## Container/Kubernetes (if applicable)

- [ ] Latest kube-bench (CIS Kubernetes Benchmark) run reviewed.
- [ ] Container images scanned (Trivy or equivalent) before deployment, with no unresolved Critical findings in production.
- [ ] Pod Security Standards/admission controls enforced on production namespaces.

## Sign-Off

| Reviewer | Date | Findings Logged to Risk Register (Y/N) |
|---|---|---|
| | | |
