# Cloud Security

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![License: CC0-1.0](https://img.shields.io/badge/license-CC0--1.0-lightgrey.svg)](LICENSE)

A curated, practitioner-oriented guide to **cloud security** — the shared responsibility model, the CSA Cloud Controls Matrix, AWS/Azure/GCP-specific security programs, Zero Trust Architecture, container and Kubernetes security, cloud security posture management, and SOC 2, with starting templates for each.

**Scope:** the cloud-specific security discipline — what changes when infrastructure is rented rather than owned, and how to secure it across the major providers and service models. General-purpose control frameworks (NIST CSF, ISO 27001, CIS Controls/Benchmarks) are covered by the companion [Security Frameworks](https://github.com/garynair/security-frameworks) list and referenced here rather than repeated; federal cloud authorization (FedRAMP) is covered by the companion [Federal Compliance](https://github.com/garynair/federal-compliance) list; cloud-focused penetration testing tools (Prowler, ScoutSuite) are covered in depth by the companion [VAPT](https://github.com/garynair/vapt) list.

**Why this list exists:** most cloud security guidance is either a single provider's own documentation (accurate but incomplete once you're multi-cloud) or a generic "cloud security best practices" listicle with no operational detail. This list is organized around the one concept that actually determines what you're responsible for — the shared responsibility model — and builds outward from there.

Contributions welcome.

---

## Contents

- [The Shared Responsibility Model](#the-shared-responsibility-model)
- [How to Approach Cloud Security](#how-to-approach-cloud-security)
- [CSA Cloud Controls Matrix (CCM) and STAR Registry](#csa-cloud-controls-matrix-ccm-and-star-registry)
- [AWS Security](#aws-security)
- [Azure Security](#azure-security)
- [Google Cloud Security](#google-cloud-security)
- [Zero Trust Architecture](#zero-trust-architecture)
- [Identity and Access Management in the Cloud](#identity-and-access-management-in-the-cloud)
- [Container and Kubernetes Security](#container-and-kubernetes-security)
- [Cloud Security Posture Management (CSPM)](#cloud-security-posture-management-cspm)
- [SOC 2 and Vendor Assurance](#soc-2-and-vendor-assurance)
- [Templates in This Repo](#templates-in-this-repo)
- [Cross-Framework Mapping and GRC Platforms](#cross-framework-mapping-and-grc-platforms)
- [Certifications and Training](#certifications-and-training)
- [Government and Standards Bodies](#government-and-standards-bodies)
- [Learning Resources](#learning-resources)
- [Related Lists](#related-lists)

---

## The Shared Responsibility Model

**What it is:** the division of security responsibility between a cloud provider and its customer, which shifts depending on the service model. The provider is always responsible for security *of* the cloud (physical data centers, host infrastructure, virtualization layer); the customer is always responsible for security *in* the cloud (their data, identities, and configuration) — but exactly where the line falls between those two moves as you go from IaaS to PaaS to SaaS.

| Layer | IaaS (e.g., EC2, Azure VMs) | PaaS (e.g., RDS, App Service) | SaaS (e.g., Microsoft 365, Salesforce) |
|---|---|---|---|
| Data classification and accountability | Customer | Customer | Customer |
| Client/endpoint protection | Customer | Customer | Customer |
| Identity and access management | Customer | Customer | Customer (configuration) |
| Application-level controls | Customer | Shared | Provider |
| Operating system | Customer | Provider | Provider |
| Network controls | Customer (with provider tools) | Shared | Provider |
| Host infrastructure | Provider | Provider | Provider |
| Physical security | Provider | Provider | Provider |

The most common, and most expensive, cloud security failures are not exotic attacks on the provider's infrastructure — they are customer-side misconfigurations (a public S3 bucket, an overly permissive IAM role, an exposed database) squarely inside the customer's half of this table.

---

## How to Approach Cloud Security

1. **Identify the service model for every cloud asset** (IaaS, PaaS, SaaS) and map it against the shared responsibility table above — you cannot secure what you don't know you're responsible for.
2. **Inventory your cloud footprint.** Multi-cloud sprawl and shadow IT (a team spinning up its own AWS account) are the norm, not the exception — an inventory tool (see CSPM below) is not optional at any real scale.
3. **Apply the relevant CIS Benchmark or provider security baseline** to every account, service, and workload — see the companion [Security Frameworks](https://github.com/garynair/security-frameworks) list for CIS Benchmarks generally, and the provider-specific sections below for AWS/Azure/GCP baselines.
4. **Enforce least-privilege identity from day one.** Cloud IAM missteps (overly broad roles, long-lived access keys, unused permissions) are the single most common root cause behind cloud breaches — see Identity and Access Management below.
5. **Turn on logging and monitoring before you need it.** CloudTrail, Azure Activity Log, and GCP Audit Logs should be enabled account-wide and shipped to a central, access-controlled log store from day one, not retrofitted after an incident.
6. **Run continuous configuration scanning (CSPM),** not a point-in-time assessment — cloud configuration drifts constantly as teams ship changes, and a scan run once a quarter will miss most of what actually goes wrong.
7. **Extend the same discipline to containers and Kubernetes** if you run them — the CIS Kubernetes Benchmark and image-scanning are not optional extras, they're the container-native version of steps 3-6 above.
8. **Get third-party assurance (SOC 2) where you're the vendor,** and require it from vendors where you're the customer — see SOC 2 and Vendor Assurance below.
9. **Feed unresolved cloud misconfigurations into the risk register** — see the companion [Risk Management](https://github.com/garynair/risk-management) list for that structure.
10. **Reassess your shared-responsibility mapping whenever you adopt a new service** — moving from a self-managed database (IaaS) to a managed one (PaaS) changes what you're responsible for, even if the data itself doesn't move.

---

## CSA Cloud Controls Matrix (CCM) and STAR Registry

**Path to adoption:** voluntary and free; STAR Registry participation (self-assessment or third-party-audited) is a public trust signal, not a formal certification in the ISO sense, though STAR Certification (combined with ISO 27001) is a recognized certification path.

- [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/) - The nonprofit that publishes the CCM, runs the STAR program, and produces widely cited cloud threat research.
- [Cloud Controls Matrix (CCM)](https://cloudsecurityalliance.org/research/cloud-controls-matrix) - CSA's free, cloud-specific control framework (17 domains) mapped to other major frameworks (NIST, ISO 27001, PCI-DSS), purpose-built to address cloud-specific risks generic frameworks don't fully cover.
- [CSA STAR Registry](https://cloudsecurityalliance.org/star) - The free, public registry where cloud providers publish self-assessments or third-party audit results against the CCM, useful both for publishing your own posture and for vetting a cloud vendor's.
- [CAIQ (Consensus Assessments Initiative Questionnaire)](https://cloudsecurityalliance.org/research/cloud-controls-matrix) - CSA's standardized vendor security questionnaire built directly on the CCM, widely accepted as an alternative to a bespoke vendor security questionnaire.

---

## AWS Security

**Path to adoption:** AWS-specific guidance is free; the tools referenced (Security Hub, Config, GuardDuty) have usage-based pricing beyond a free tier.

- [AWS Well-Architected Framework — Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) - AWS's own design-principles guidance for identity, detective controls, infrastructure protection, data protection, and incident response, the closest thing to an official AWS security methodology.
- [AWS Security Hub](https://aws.amazon.com/security-hub/) - AWS's native security posture management service, aggregating findings from GuardDuty, Config, Inspector, and third-party tools, with built-in checks against the AWS Foundational Security Best Practices standard and CIS AWS Foundations Benchmark.
- [CIS Amazon Web Services Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services) - The CIS hardening benchmark specific to core AWS account and service configuration, also referenced in the companion Security Frameworks list's general CIS Benchmarks coverage.
- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) - AWS's official, detailed guidance on least-privilege roles, avoiding long-lived access keys, and using IAM Identity Center for federated access.

---

## Azure Security

**Path to adoption:** free guidance; Microsoft Defender for Cloud has a free tier (foundational CSPM) with paid tiers for enhanced/workload-specific protection.

- [Microsoft Cloud Security Benchmark (MCSB)](https://learn.microsoft.com/en-us/security/benchmark/azure/introduction) - Microsoft's own cross-cloud security baseline (superseding the earlier Azure Security Benchmark), covering identity, network, data protection, and more, mapped to CIS Controls and NIST SP 800-53.
- [Microsoft Defender for Cloud](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction) - Microsoft's native CSPM and cloud workload protection platform, providing a free Secure Score against the MCSB and paid workload-specific threat protection.
- [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure) - The CIS hardening benchmark specific to core Azure tenant and subscription configuration.
- [Microsoft Entra ID (Azure AD) Security Best Practices](https://learn.microsoft.com/en-us/entra/architecture/security-operations-introduction) - Microsoft's official guidance for securing the identity layer that underpins nearly every other Azure control.

---

## Google Cloud Security

**Path to adoption:** free guidance; Security Command Center has a free tier (basic) with a paid Premium/Enterprise tier for deeper threat detection and posture management.

- [Google Cloud Security Best Practices Center](https://cloud.google.com/security/best-practices) - Google's official hub of security guidance across identity, network, data, and application layers.
- [Security Command Center](https://cloud.google.com/security-command-center) - Google Cloud's native security and risk management platform, providing asset inventory, misconfiguration detection, and threat detection.
- [CIS Google Cloud Platform Foundation Benchmark](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) - The CIS hardening benchmark specific to core GCP organization and project configuration.
- [Google Cloud Architecture Framework — Security](https://cloud.google.com/architecture/framework/security) - Google's design-principles guidance, the GCP counterpart to AWS's Well-Architected security pillar and Microsoft's MCSB.

---

## Zero Trust Architecture

**What it's for:** an architectural model built on "never trust, always verify" — no user, device, or workload is trusted by default based on network location alone, even inside a traditional perimeter. Cloud environments (where there often is no clean network perimeter to begin with) are one of the primary drivers behind Zero Trust's adoption.

- [NIST SP 800-207](https://csrc.nist.gov/pubs/sp/800/207/final) - "Zero Trust Architecture," NIST's foundational, vendor-neutral definition of Zero Trust tenets and reference architectures, the standard most vendor Zero Trust marketing ultimately points back to.
- [CISA Zero Trust Maturity Model](https://www.cisa.gov/zero-trust-maturity-model) - CISA's practical maturity model (Traditional, Initial, Advanced, Optimal) across five pillars (Identity, Devices, Networks, Applications and Workloads, Data), useful for benchmarking progress rather than just defining the end state.

---

## Identity and Access Management in the Cloud

**How to do it well:**
1. Default every role and policy to least privilege, and treat "grant broad access to unblock someone quickly" as a tracked exception with an expiry, not a permanent fix.
2. Eliminate long-lived credentials wherever possible — use short-lived, automatically rotated credentials (IAM roles assumed by workloads, federated human access via SSO) instead of static access keys.
3. Require multi-factor authentication for every human identity with console or API access, with no exceptions for "convenience" accounts.
4. Use Privileged Access Management (PAM) or just-in-time elevation for any identity with administrative-tier permissions, so standing admin access is the exception, not the default.
5. Review access permissions on a recurring cadence (see the companion Risk Management list's periodic review guidance) and immediately upon role change or offboarding.
6. Centralize identity across a multi-cloud estate through a single IdP (federated SSO) rather than maintaining separate identity stores per cloud provider.

- [NIST SP 800-63](https://pages.nist.gov/800-63-4/) - NIST's Digital Identity Guidelines, the foundational federal reference for identity proofing, authentication, and federation, applicable well beyond cloud specifically.

---

## Container and Kubernetes Security

**Path to adoption:** free and open-source tooling covers most of this; managed Kubernetes (EKS, AKS, GKE) shifts control-plane security to the provider per the shared responsibility model, but workload and configuration security remains the customer's.

- [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes) - The definitive hardening benchmark for Kubernetes control-plane and worker-node configuration, also referenced in the companion Security Frameworks list.
- [kube-bench](https://github.com/aquasecurity/kube-bench) - Free, open-source tool that automates checking a Kubernetes cluster against the CIS Kubernetes Benchmark.
- [Trivy](https://github.com/aquasecurity/trivy) - Free, open-source vulnerability and misconfiguration scanner for container images, filesystems, and infrastructure-as-code, one of the most widely adopted tools in this space.
- [NSA/CISA Kubernetes Hardening Guidance](https://www.cisa.gov/resources-tools/resources/kubernetes-hardening-guidance) - A free, detailed joint hardening guide covering threat scenarios specific to Kubernetes beyond what the CIS Benchmark's configuration checks capture.

---

## Cloud Security Posture Management (CSPM)

**Path to adoption:** free and open-source options exist alongside a large commercial CSPM market; start with the free tools before evaluating a paid platform.

- [Prowler](https://github.com/prowler-cloud/prowler) - Free, open-source, multi-cloud (AWS, Azure, GCP, Kubernetes) security and compliance scanner, mapped to CIS, NIST, and other frameworks. Also referenced in the companion [Security Frameworks](https://github.com/garynair/security-frameworks) and [VAPT](https://github.com/garynair/vapt) lists.
- [ScoutSuite](https://github.com/nccgroup/ScoutSuite) - Free, open-source multi-cloud security auditing tool from NCC Group producing a single consolidated report across providers.
- [Steampipe](https://steampipe.io/) - Free, open-source tool that queries cloud infrastructure (and dozens of other services) using SQL, commonly used to build custom CSPM-style compliance checks.
- [Wiz](https://www.wiz.io/) - Leading commercial cloud-native application protection platform (CNAPP), combining CSPM, vulnerability management, and workload protection in an agentless model.

---

## SOC 2 and Vendor Assurance

**What it is:** a SOC 2 report is an independent auditor's attestation of a service organization's controls against the AICPA's Trust Services Criteria (Security, Availability, Processing Integrity, Confidentiality, Privacy — Security is mandatory, the other four are selected based on scope). It's the report cloud/SaaS vendors most commonly provide to prove their security posture to customers, and the one most commonly requested during vendor due diligence.

**How to use it (as the vendor being assessed, or the customer requesting it):**
1. If you're the vendor: start with a Type I report (controls exist and are suitably designed at a point in time) if you have no prior audit history, then progress to a Type II (controls also operated effectively over a period, typically 6-12 months) — most enterprise customers require Type II specifically.
2. If you're the customer: always request the Type II report over Type I where available, and read the auditor's opinion and any noted exceptions, not just the cover letter.
3. Map SOC 2 Trust Services Criteria to your own control framework (NIST CSF, ISO 27001) — significant overlap exists, and a SOC 2 audit can often reuse evidence already collected for another framework.
4. Track SOC 2 report expiry/renewal dates for every critical vendor as part of the Third-Party and Vendor Risk Management process in the companion [Risk Management](https://github.com/garynair/risk-management) list — a stale SOC 2 report is not current assurance.

- [AICPA SOC 2 Overview](https://www.aicpa-cima.com/topic/audit-assurance/audit-and-assurance-greater-than-soc-2) - The AICPA's own overview of the SOC 2 framework, the Trust Services Criteria, and Type I vs. Type II distinctions.
- [AICPA Trust Services Criteria](https://www.aicpa-cima.com/resources/download/2017-trust-services-criteria-with-revised-points-of-focus-2022) - The full, current criteria document defining exactly what a SOC 2 audit tests against.

---

## Templates in This Repo

- [`templates/shared-responsibility-matrix.csv`](templates/shared-responsibility-matrix.csv) - A fillable shared-responsibility matrix by service model and control area, built on the table in this README.
- [`templates/cloud-config-review-checklist.md`](templates/cloud-config-review-checklist.md) - A recurring cloud account configuration review checklist covering IAM, logging, network, and data-protection basics across major providers.

---

## Cross-Framework Mapping and GRC Platforms

- [Secure Controls Framework (SCF)](https://securecontrolsframework.com/) - Free, open (Creative Commons) meta-framework mapping outward to 250+ laws, regulations, and frameworks. Shared with the companion [Security Frameworks](https://github.com/garynair/security-frameworks) list.
- [Wiz](https://www.wiz.io/) - Commercial CNAPP unifying CSPM, vulnerability, and identity risk across multi-cloud environments.
- [Orca Security](https://orca.security/) - Commercial agentless cloud security platform combining CSPM, vulnerability management, and compliance monitoring.
- [Vanta](https://www.vanta.com/) - Commercial compliance-automation platform with continuous cloud-configuration monitoring feeding SOC 2, ISO 27001, and other framework evidence pipelines.

---

## Certifications and Training

- [CCSK (Certificate of Cloud Security Knowledge)](https://cloudsecurityalliance.org/education/ccsk/) - CSA's vendor-neutral cloud security certification, built directly on the CCM and widely considered the foundational cloud security credential.
- [CCSP (Certified Cloud Security Professional)](https://www.isc2.org/certifications/ccsp) - (ISC)²'s advanced, vendor-neutral cloud security certification, comparable in rigor to the CISSP but cloud-focused.
- [AWS Certified Security — Specialty](https://aws.amazon.com/certification/certified-security-specialty/) - AWS's own specialty certification validating deep AWS-specific security implementation knowledge.
- [Microsoft Certified: Azure Security Engineer Associate](https://learn.microsoft.com/en-us/credentials/certifications/azure-security-engineer/) - Microsoft's Azure-specific security certification.
- [Google Cloud Certified Professional Cloud Security Engineer](https://cloud.google.com/learn/certification/cloud-security-engineer) - Google's GCP-specific security certification.

---

## Government and Standards Bodies

- [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/) - The nonprofit publishing the CCM, STAR Registry, and widely cited cloud threat research.
- [CISA](https://www.cisa.gov/) - Publisher of the Zero Trust Maturity Model and joint Kubernetes hardening guidance referenced above.
- [NIST Computer Security Resource Center (CSRC)](https://csrc.nist.gov/) - Publisher of SP 800-207 (Zero Trust Architecture) and SP 800-63 (Digital Identity Guidelines).
- [American Institute of CPAs (AICPA)](https://www.aicpa-cima.com/) - Owner of the SOC 2 framework and Trust Services Criteria.

---

## Learning Resources

- [CSA Research Library](https://cloudsecurityalliance.org/research/) - Free access to CSA's cloud threat research, guidance documents, and working-group publications.
- [AWS Well-Architected Framework — Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/welcome.html) - Free, detailed, scenario-driven AWS security guidance.
- [Microsoft Learn: Azure Security](https://learn.microsoft.com/en-us/training/browse/?products=azure&subjects=security) - Free, structured Azure security training paths tied directly to the Azure certifications above.
- [Google Cloud Skills Boost — Security](https://www.cloudskillsboost.google/catalog?keywords=security) - Free and paid GCP-specific security training paths tied to the Professional Cloud Security Engineer certification.

---

## Related Lists

- [Security Frameworks](https://github.com/garynair/security-frameworks) - A companion curated list covering NIST CSF, ISO/IEC 27001, PCI-DSS, CIS Controls/Benchmarks, and DISA STIG — the general-purpose baselines this list's provider-specific guidance builds on.
- [VAPT](https://github.com/garynair/vapt) - A companion curated list covering Prowler, ScoutSuite, and other cloud-focused testing tools in the context of an authorized penetration-testing engagement.
- [Federal Compliance](https://github.com/garynair/federal-compliance) - A companion curated list covering FedRAMP, the federal-specific cloud authorization program this list's general cloud security practices feed into.
- [Risk Management](https://github.com/garynair/risk-management) - A companion curated list covering the risk register and third-party/vendor risk process that cloud misconfigurations and vendor SOC 2 reviews feed into.

---

## Contributing

PRs welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for the criteria a new entry must meet.

## License

This list is published under [CC0 1.0 Universal](LICENSE). The linked resources retain their own licenses. Templates in the `templates/` directory are original works released under the same CC0 license — use, modify, and redistribute them freely.
