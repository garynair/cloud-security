# Contributing

Thank you for considering a contribution. This list covers **cloud security**: the shared responsibility model, the CSA Cloud Controls Matrix, AWS/Azure/GCP-specific security programs, Zero Trust Architecture, container/Kubernetes security, cloud security posture management, and SOC 2.

## What belongs here

- Official provider security documentation and design frameworks (AWS Well-Architected, Azure MCSB, GCP Architecture Framework).
- Free or open-source CSPM, container, and Kubernetes security tooling.
- Standards and frameworks specific to cloud (CSA CCM, NIST SP 800-207, SOC 2).
- Templates that help a practitioner start a shared-responsibility mapping or configuration review rather than build one from scratch.
- Commercial cloud security platforms with a named, cloud-specific feature set.
- Certification and training paths specific to cloud security.

## What does not belong here

- General-purpose control frameworks (NIST CSF, ISO 27001, CIS Controls generally) — covered by the companion [Security Frameworks](https://github.com/garynair/security-frameworks) list; reference them, don't duplicate them.
- FedRAMP and federal cloud authorization — covered by the companion [Federal Compliance](https://github.com/garynair/federal-compliance) list.
- Offensive/testing use of cloud scanning tools in an engagement context — covered by the companion [VAPT](https://github.com/garynair/vapt) list.
- Vendor marketing content without a substantive free tier, open-source component, or named feature.
- Self-promotion of products you have not used or do not work with.

## How to submit

1. Open an issue titled `[Submission]: Resource Name`, or open a PR directly.
2. Add the entry in alphabetical order within the relevant section, in the format: `- [Name](URL) - one sentence on what it is, one sentence on why a practitioner would care.`
3. If you are the author or maintainer of the resource, disclose that in the issue or PR description.

## Style

- American English in the description copy.
- No emojis.
- Avoid banned vocabulary (delve, leverage, harness, robust, seamless, holistic, transformative, paradigm). Plain language wins.

## Quality bar

- Must be publicly accessible (official source, free tier, or open-source).
- Must be actively maintained (commit within 12 months, or a provider/standards body with an active update cycle).
- Description must be factual, with no performance claims that cannot be verified.

## Review

A maintainer reviews PRs within seven days. Most PRs that meet the criteria above land within two weeks.

## License

By contributing you agree your contribution is released under CC0 1.0 Universal.
