# Azure CSPM Fix Plan by Service

*Based on COSS_CSPM_Report (1).docx*

| High findings | Distinct controls | Safe to fix now | Real blockers |
| :---: | :---: | :---: | :---: |
| **161** | **33** | **67** | **65** |

> **A note on cost figures:** Cost figures in this document are **ballpark estimates from general Azure pricing patterns, not quoted prices**. Verify exact numbers in the Azure Pricing Calculator before committing budget, since pricing varies by region and changes over time.

## 1. At a Glance

### Risk legend

| **Rating** | **Meaning** |
| --- | --- |
| **SAFE** | No functional risk. Fix anytime, config-only. |
| **NEEDS PLANNING** | Safe in principle, but breaks something if applied without a decision or prep step first (e.g. a missing IP allowlist). |
| **BLOCKER** | Will break live functionality unless a bigger architecture change happens first. |
| **ALREADY FIXED** | Real exposure already closed; only the literal compliance flag remains. |

### Breakdown by risk level

| **Risk** | **Resources** | **Share of total** |
| --- | --- | --- |
| **ALREADY FIXED** | **1** | █  1%  ·  1 control |
| **SAFE** | **67** | ██████████████████████  42%  ·  20 controls |
| **NEEDS PLANNING** | **28** | █████████  17%  ·  6 controls |
| **BLOCKER** | **65** | ██████████████████████  40%  ·  7 controls |
| **Total** | **161** | 33 controls |

*161 = 1 (Cognitive Services) + 21 (Compute) + 6 (Kubernetes) + 2 (Monitor) + 26 (Network) + 24 (Security Center) + 81 (Storage), which ties out exactly to the report's per-service severity summary.*

## 2. All 161 High Findings

| **#** | **Service** | **Control** | **Res.** | **Risk** | **Why** |
| --- | --- | --- | --- | --- | --- |
| 3 | **Cognitive Services** | Cognitive Services accounts should disable public network access | **1** | **ALREADY FIXED** | VNet rules already deny public access live; only the literal compliance flag is unflipped |
| 5 | **Compute** | All network ports should be restricted on NSGs associated to your VM | **2** | **NEEDS PLANNING** | Needs a port-usage audit first, or something real gets cut off |
| 6 | **Compute** | Audit Linux machines that allow remote connections from accounts without passwords | **6** | **SAFE** | Just confirm SSH keys exist before disabling password auth |
| 9 | **Compute** | Ensure 'Disk Network Access' is NOT set to 'Enable public access from all networks' | **6** | **SAFE** | Disk export only, doesn't touch app traffic |
| 12 | **Compute** | Internet-facing virtual machines should be protected with network security groups | **1** | **SAFE** | Additive, doesn't remove any existing access |
| 16 | **Compute** | System updates should be installed on your machines | **6** | **SAFE** | Just schedule a maintenance window |
| 20 | **Kubernetes Service** | Authorized IP ranges should be defined on Kubernetes Services | **2** | **NEEDS PLANNING** | Breaks CI/CD unless runner IP is allow-listed first; impractical if deploy path is GitHub-hosted OIDC (dynamic IPs) |
| 25 | **Kubernetes Service** | Kubernetes clusters should have logging enabled | **2** | **SAFE** | Visibility only |
| 26 | **Kubernetes Service** | Kubernetes clusters should have network policy enabled | **2** | **NEEDS PLANNING** | Default-deny breaks Prometheus scraping Kafka/JanusGraph across namespaces unless NetworkPolicies are written first |
| 31 | **Monitor** | Azure subscriptions should have a log profile for Activity Log | **1** | **SAFE** | Visibility only |
| 45 | **Monitor** | Ensure a 'Diagnostic Setting' exists for Subscription Activity Logs | **1** | **SAFE** | Visibility only |
| 48 | **Network** | Ensure HTTP(S) access from the Internet is evaluated and restricted | **4** | **BLOCKER** | nginx-public-ingress must stay open to learners; needs manual per-NSG review, not a bulk flip |
| 51 | **Network** | Ensure SSH access is restricted from the internet | **11** | **NEEDS PLANNING** | Admin-only, no app impact, but needs the team's real source IPs first |
| 53 | **Network** | Management ports should be closed on your virtual machines | **11** | **NEEDS PLANNING** | Same as SSH; also covers Pritunl VPN ports on the same NSGs |
| 59 | **Security Center** | Azure Defender for Azure SQL Database servers should be enabled | **1** | **SAFE** | Pure monitoring, no functional impact |
| 61 | **Security Center** | Azure Defender for Key Vault should be enabled | **1** | **SAFE** | Pure monitoring, no functional impact |
| 62 | **Security Center** | Azure Defender for Kubernetes should be enabled | **1** | **SAFE** | Pure monitoring, no functional impact |
| 64 | **Security Center** | Azure Defender for SQL should be enabled for unprotected SQL Managed Instances | **1** | **SAFE** | Pure monitoring, no functional impact |
| 66 | **Security Center** | Azure Defender for servers should be enabled | **1** | **SAFE** | Pure monitoring, no functional impact |
| 70 | **Security Center** | Ensure Microsoft Defender CSPM is set to 'On' | **1** | **SAFE** | Pure monitoring, no functional impact |
| 77 | **Security Center** | Microsoft Defender for Containers should be enabled | **1** | **SAFE** | Pure monitoring, no functional impact |
| 78 | **Security Center** | Microsoft Defender for Storage (Classic) should be enabled | **1** | **SAFE** | Pure monitoring, no functional impact |
| 80 | **Security Center** | Security center pricing should be set to standard | **16** | **SAFE** | Tier switch the plans above depend on; cost only |
| 82 | **Storage** | Default network access rule for storage accounts should be set to deny | **4** | **BLOCKER** | Breaks public content reads **and** direct-to-blob uploads (SAS URLs); no CDN/upload proxy yet |
| 87 | **Storage** | Ensure Soft Delete is Enabled for Azure Containers and Blob Storage | **5** | **SAFE** | Zero functional impact; confirmed still off on the real content accounts |
| 91 | **Storage** | Ensure soft delete is enabled for Azure Storage | **5** | **SAFE** | Zero functional impact; confirmed still off on the real content accounts |
| 92 | **Storage** | Ensure 'Allow Blob Anonymous Access' is set to 'Disabled' for storage accounts | **2** | **BLOCKER** | Breaks anonymous public content reads; same root cause as the 39-container finding |
| 94 | **Storage** | Ensure 'Public access level' is set to Private for blob containers | **39** | **BLOCKER** | Breaks public content loading for every learner; needs CDN/Front Door or SAS-token reads first |
| 95 | **Storage** | Ensure soft delete for containers on Azure Blob Storage is Enabled | **5** | **SAFE** | Zero functional impact; confirmed still off on the real content accounts |
| 97 | **Storage** | Public network access should be disabled for storage accounts | **6** | **BLOCKER** | Breaks public content reads **and** direct-to-blob uploads, same as #82 |
| 99 | **Storage** | Soft delete for blobs should be enabled | **5** | **SAFE** | Zero functional impact; confirmed still off on the real content accounts |
| 101 | **Storage** | Storage account public access should be disallowed | **6** | **BLOCKER** | Breaks reads **and** uploads permanently, same as #82/#97 |
| 103 | **Storage** | Storage accounts should restrict network access | **4** | **BLOCKER** | Same control family as #82; same two blockers (reads + uploads) |

## 3. Detail by Service

Each control below covers the same four things: **why fix it** (the actual risk today), **impact if fixed** (what changes once applied), **risk / why hold off** (what breaks if done carelessly, if anything), and **cost**. Labels are colour-coded throughout:

**Why fix** · **Impact if fixed** · **Risk / why hold off** · **Cost** · **Verified live** · **Notes**

### Cognitive Services

#### Cognitive Services accounts should disable public network access (1 resource) — `ALREADY FIXED`

- **Checked live:** Checked the actual live resource, not just the portal screenshot (az cognitiveservices account show -g ed-coss -n cossdev-semanticsearch), since the portal view only shows which options exist, not necessarily which one is active:

  ```
  publicNetworkAccess: Enabled
  networkAcls.defaultAction: Deny
  virtualNetworkRules: [ed-dev-aks, ed-sandbox-aks]   ← only these two
  ipRules: []
  ```

- **What this actually means:** defaultAction: Deny is already active right now; the account is **not** open to the internet. Only the two real AKS clusters (ed-dev, ed-sandbox, confirmed on separate VNets, both genuine callers since test.sunbirded.org runs on ed-sandbox) can reach it. This is a VNet Service Endpoint rule (restricts by source-subnet identity over the Azure backbone), not a Private Endpoint, and it's genuinely enforced. Both subnets have the Microsoft.CognitiveServices service endpoint actually enabled (provisioningState: Succeeded).
- **Why fix:** Why the CSPM report still flags it High: the control checks the literal publicNetworkAccess property, which is still "Enabled", a separate field from networkAcls.defaultAction. By the tool's strict definition this is non-compliant, even though the practical risk is already closed. **This is a compliance-flag gap, not a live security gap.**
- **Impact if fixed:** To fully close the flag, flip publicNetworkAccess to Disabled. Once that happens, VNet Service Endpoint rules stop applying, so it would need genuine Private Endpoints + Private DNS Zones for both VNets first (~$7.30/month each, ~$14.60/month total), then a connectivity test from inside both clusters. Not urgent given the real exposure is already closed.
- **Cost:** **$0 today**; the current VNet Service Endpoint protection is free. The optional last step would be ~$14.60/month if ever pursued.

### Compute

#### All network ports should be restricted on NSGs (2 resources) — `NEEDS PLANNING`

- **Why fix:** Wide-open NSG rules (e.g. any-to-any) expand attack surface unnecessarily.
- **Impact if fixed:** Only the specific ports each VM actually needs stay open.
- **Risk / why hold off:** Needs a real audit of what ports are in legitimate use on those 2 VMs first. Restrict blind and something real gets cut off.
- **Cost:** Free, config-only.

#### Audit Linux machines allowing password-based remote connections (6 resources) — `SAFE`

- **Why fix:** Password auth over SSH is brute-forceable; key-based auth isn't.
- **Impact if fixed:** SSH login only via key pairs, no functional impact on any app.
- **Risk / why hold off:** None, as long as whoever administers these 6 machines already has SSH keys set up before password auth is turned off.
- **Cost:** Free, config-only.

#### Disk Network Access should not allow public access from all networks (6 resources) — `SAFE`

- **Why fix:** This is about managed-disk export endpoints (snapshots/backups), not app traffic. Leaving it public is unnecessary exposure.
- **Impact if fixed:** Disk export only works from inside the VNet or via explicit private endpoint; doesn't touch the running VM's app traffic.
- **Verified live, exact 6 disks:** az disk list --query "[].{name:name, networkAccessPolicy:networkAccessPolicy}" shows all 6 standalone VMs' OS disks are AllowAll:
  - coss-jenkins_OsDisk_1_...
  - coss-timesheet_OsDisk_1_...
  - ed-dev-runner_OsDisk_1_...
  - rc-dev_OsDisk_1_...
  - ed-sandbox-runner_OsDisk_1_...
  - rc-sandbox_OsDisk_1_...
- **Note:** Every AKS-managed pvc-* disk is already DenyAll by default; nothing AKS-related needs touching.
- **Risk / why hold off:** None for the app. Only matters if something outside the VNet currently exports these disks directly (rare).
- **Cost:** Free, config-only.

#### Internet-facing VMs should be protected by NSGs (1 resource) — `SAFE`

- **Why fix:** Confirms the one internet-facing VM has an NSG attached at all, not that its access is removed.
- **Impact if fixed:** Adds a network security group in front of the VM; doesn't change what's exposed, just ensures a rule set governs it.
- **Risk / why hold off:** None. This is additive, though the NSG rules still need to allow what's actually needed (same audit caveat as above).
- **Cost:** Free, config-only.

#### System updates should be installed on your machines (6 resources) — `SAFE`

- **Why fix:** Unpatched OS-level vulnerabilities are a common way in.
- **Impact if fixed:** The 6 VMs get pending OS patches applied.
- **Risk / why hold off:** Low, but any patch batch carries a small chance of a service needing a restart. Apply in a maintenance window, not mid-day.
- **Cost:** Free; only cost is the ops time to schedule a patch window.

### Kubernetes Service

#### Authorized IP ranges should be defined on Kubernetes Services (2 resources) — `NEEDS PLANNING`

- **Why fix:** The AKS **API server** (kubectl/admin access) is currently reachable from any IP. Only credentials stand between an attacker and cluster admin.
- **Impact if fixed:** Only allow-listed IPs can talk to the API server; app/user traffic is completely unaffected (different endpoint from the app's ingress).
- **Risk / why hold off:** Depends on which deploy path is in use (checked against private-repo-setup/README.md). The repo documents two CI/CD paths that behave very differently here:
  - **Self-hosted runner + Managed Identity** (recommended path, README.md:28): the runner is a VM inside the same VNet with a fixed IP. "Add the runner IP to the allowlist" is simple and accurate, possibly not even needed if the VM is VNet-integrated.
  - **GitHub Actions OIDC path** (README.md:14): GitHub-hosted runners (ubuntu-latest) use **dynamic, ephemeral IPs from GitHub's huge published range**. Allow-listing that range mostly defeats the purpose. If this is the deploy path, it needs a self-hosted runner inside the VNet instead, not an IP allowlist.
- **Cost:** Free, config-only (self-hosted path); the OIDC path would need the cost of standing up a self-hosted runner if switched.

#### Kubernetes clusters should have logging enabled (2 resources) — `SAFE`

- **Why fix:** Without control-plane logging there's no audit trail of who did what to the cluster.
- **Impact if fixed:** API server audit logs start flowing to Log Analytics.
- **Risk / why hold off:** None.
- **Cost:** Log Analytics ingestion, roughly ~$2.30/GB; audit log volume is typically small.

#### Kubernetes clusters should have network policy enabled (2 resources) — `NEEDS PLANNING`

- **Why fix:** Without a network policy engine, any pod can talk to any other pod in the cluster; no segmentation between workloads.
- **Impact if fixed:** Turning on the *engine* (Calico / Azure NPM) with everything still default-allow is safe by itself and changes nothing yet.
- **Risk / why hold off:** Confirmed in code, not assumed. Existing NetworkPolicy manifests only exist inside third-party subcharts (Kafka, Kong, JanusGraph, Loki, Grafana, prometheus-operator, etc.); none are written for this project's own cross-namespace flows.
  - **Monitoring → sunbird (the real blocker):** helmcharts/monitoring/charts/kube-prometheus-stack/values.yaml sets serviceMonitorNamespaceSelector: {} / podMonitorNamespaceSelector: {}, so Prometheus scrapes monitors in **every** namespace. Kafka and JanusGraph both expose prometheus.io/scrape: "true" and get scraped directly over pod IP from the monitoring namespace. Default-deny would cut this off immediately.
  - **Velero → sunbird (weaker concern):** helmcharts/additional/values.yaml targets includedNamespaces: ["sunbird"], but that's mostly Kubernetes-API-driven plus egress to Azure Blob. It needs egress rules to the API server and Azure Storage, not a pod-to-pod allow rule.
- **Cost:** Free, config-only; the cost is engineering time to write and test the NetworkPolicies (at minimum: ingress-allow from monitoring to scraped ports on sunbird/knowledgebb pods, and egress-allow from velero to the API server + Azure Storage).

### Monitor

#### Azure subscriptions should have a log profile for Activity Log (1 resource) — `SAFE`

- **Why fix:** Without this, subscription-level activity (who changed what, when) isn't retained anywhere.
- **Impact if fixed:** Activity log entries get archived/retained per the profile's settings.
- **Risk / why hold off:** None.
- **Cost:** Negligible; storage for log archival.

#### Ensure a 'Diagnostic Setting' exists for Subscription Activity Logs (1 resource) — `SAFE`

- **Why fix:** Same gap from a different angle: without a diagnostic setting, activity logs aren't routed anywhere for alerting/analysis.
- **Impact if fixed:** Activity logs start flowing to Log Analytics/Storage for querying and alerting.
- **Risk / why hold off:** None.
- **Cost:** Negligible; the first several alert rules and modest log volume are typically low/no cost.

### Network

#### Ensure HTTP(S) access from the Internet is evaluated and restricted (4 resources) — `BLOCKER`

- **Why fix:** Flags NSGs that allow HTTP/HTTPS from any source IP without review.
- **Impact if fixed:** Tightens NSG rules to only what's actually needed.
- **Risk / why hold off:** nginx-public-ingress is *supposed* to be internet-facing; it's the platform's entire public entrypoint for learners. This finding almost certainly means "don't have overly broad NSG rules elsewhere," not "block public HTTP to the ingress." **Misapplying it to the public ingress takes the whole platform offline.** Needs manual, per-NSG review, not a bulk fix.
- **Cost:** Free, config-only, but the review time is the real cost here.

#### Ensure SSH access is restricted from the internet (11 resources) — `NEEDS PLANNING`

- **Why fix:** SSH open to any IP is a standing brute-force/credential-stuffing target.
- **Impact if fixed:** SSH only reachable from allow-listed IPs (or via Bastion/VPN).
- **Verified live (a literal wildcard):** az network nsg rule list shows port 22 allowed from source * on **11 NSGs**:
  - coss-jenkins-nsg, coss-jenkins-2.346.3-nsg, coss-jenkins-test-nsg
  - cossjenkinsnsg329 (also has port 8080 open from *)
  - cossjenkinsnsg349, cossjenkinsnsg397, cossjenkinsnsg709, cossjenkinsnsg948, cossjenkinstestnsg216
  - ed-dev-runnerNSG, ed-sandbox-runnerNSG
- **Risk / why hold off:** Admin access only, no functional risk to Spark itself, but the team's actual source IPs must be identified first or legitimate admins get locked out too.
- **Cost:** Free, config-only.

#### Management ports should be closed on your virtual machines (11 resources) — `NEEDS PLANNING`

- **Why fix:** Same class of issue as SSH above, covering RDP/WinRM/other management ports too.
- **Impact if fixed:** Management ports only reachable from allow-listed sources.
- **Verified live:** The 2 runner NSGs also have VPN/admin ports open from *, worth closing in the same pass:
  - ed-dev-runnerNSG and ed-sandbox-runnerNSG: 1194 (wireguard), 443 (pritunl-ui), 12535 (openvpn)
  - ed-sandbox-runnerNSG additionally: 12548 (pritunl-vpn), 11485 (wireguard-wg)
- **Note:** These back the Pritunl VPN setup (vpn_enabled: true path). Restricting by source IP is the same admin-IP-allowlist exercise as SSH, not a separate project.
- **Risk / why hold off:** Same as SSH: admin-only, no app impact, just needs the real admin IP list gathered first.
- **Cost:** Free, config-only.

### Security Center

All 9 controls in this section are the same shape: flip a Defender plan or the pricing tier to Standard. None of them touch how the application runs; they are pure monitoring/alerting additions.

| **Control** | **Res.** | **Why fix** | **Cost** |
| --- | --- | --- | --- |
| **Defender for Azure SQL Database servers** | **1** | Detects SQL-specific threats (injection attempts, anomalous access) | **~$15/vCore/month** |
| **Defender for Key Vault** | **1** | Alerts on suspicious Key Vault access (where secrets/certs live) | **~$0.02/10K transactions** |
| **Defender for Kubernetes** | **1** | Detects AKS-level threats (anomalous pod behaviour, exposed dashboards) | **~$7/vCore/month** |
| **Defender for SQL Managed Instances** | **1** | Same SQL threat detection, for managed instances | **~$15/vCore/month** |
| **Defender for servers** | **1** | Endpoint detection/response on the VMs | **~$15/VM/month (Plan 2)** |
| **Defender CSPM** | **1** | Continuous posture scanning (effectively what generated this report) | **~$5/billable resource/month** |
| **Defender for Containers** | **1** | Image vulnerability scanning + runtime threat detection | **Per image scan or per vCore** |
| **Defender for Storage (Classic)** | **1** | Detects anomalous access patterns on storage accounts (relevant given public-blob usage) | **~$10/storage account/month** |
| **Security center pricing → standard** | **16** | Underlying tier switch that several Defender plans above depend on | **Bundled into the plan costs above** |

- **Impact if fixed (all 9):** Defender starts generating security alerts/recommendations in the portal. Visibility only; doesn't change how any service runs, traffic, or config.
- **Risk / why hold off:** None functionally. The only real consideration is cost, since this is recurring per-resource billing, not a one-time fee.

### Storage

#### Default network access rule should be set to deny (4 resources) — `BLOCKER`

- **Why fix:** Storage accounts currently accept requests from any network by default; deny-by-default with explicit exceptions is the standard posture.
- **Impact if fixed:** Only explicitly allowed networks/services can reach the storage account at all.
- **Risk / why hold off:** Two separate blockers, not one:
  - **Reads:** Sunbird-Spark serves learner-facing content (videos, images, course material) via **direct, anonymous public blob URLs**. A blanket deny rule breaks content loading for every learner unless a CDN/Front Door is put in front first (or SAS-token URLs are adopted for reads).
  - **Uploads (confirmed in code):** Kong exposes signed-upload-URL endpoints (uploadUrlAsset, uploadUrlContentV2/V3, getContentUploadUrl, getFileUploadUrls in helmcharts/edbb/charts/kong-apis/configs/kong-apis.yaml). knowlg generates a time-limited Azure AD User Delegation SAS URL (upload.url.ttl, cloud_storage_auth_type: OIDC, shared_access_key_enabled = false in opentofu/azure/modules/storage/main.tf) and the **browser PUTs directly to Blob Storage**, confirmed by the CORS rule (allowed_origins: ["*"], allowed_methods includes PUT).
  - A network ACL sits *below* the authentication layer; a valid signed URL doesn't help if the network rule denies the client's IP. **This breaks content authoring, not just content reading.**
- **Cost:** Free to flip, but the real fix (CDN/Front Door for reads, plus a VNet-integrated upload proxy or a backend-mediated relay) adds recurring bandwidth-based cost and real engineering work.

#### Soft Delete enabled (4 separate controls, same fix) (20 resources) — `SAFE`

- **Covers:** Containers/Blob Storage (5), Azure Storage (5), containers on Blob Storage (5), blobs (5).
- **Why fix:** Without soft delete, an accidental or malicious delete of a blob/container is unrecoverable.
- **Impact if fixed:** Deleted blobs/containers are recoverable for the configured retention window; zero change to how reads/writes work.
- **Risk / why hold off:** None.
- **Verified live, not yet fixed:** az storage account blob-service-properties show per account: only edcossda72f12a (the Kimai backup account) has it on. eddevda72f12a and edsandboxda72f12a, **the two accounts actually serving learner content, both have it off**, as do the tfstate and legacy sunbirdstagingpublic accounts. This is genuinely unprotected right now.
- **Cost:** Negligible; retains deleted data for the retention window, minor storage cost.

#### 'Allow Blob Anonymous Access' should be Disabled (2 resources) — `BLOCKER`

- **Why fix:** This is the account-level switch that permits anonymous container-level access to exist at all; a broader exposure than any single container's setting.
- **Impact if fixed:** No container on these accounts can be set to public, even accidentally, going forward.
- **Risk / why hold off:** If any learner-facing public containers live on these 2 accounts, disabling this **immediately breaks anonymous access to that content**. Same root cause as the "Public access level" finding; needs the same CDN/SAS-token fix first.
- **Cost:** Free to flip, but the real fix (CDN/SAS tokens) has its own recurring cost.

#### 'Public access level' set to Private for blob containers (39 resources) — `BLOCKER`

- **Biggest finding:** The single largest finding in the whole report.
- **Why fix:** 39 containers currently allow anonymous public read; the largest exposed surface in the account.
- **Impact if fixed:** None of those 39 containers would serve content to an unauthenticated request anymore.
- **Verified live, exact containers:**
  - eddevda72f12a: ed-dev-public-0c422ccdbf (blob)
  - edsandboxda72f12a: ed-sandbox-dial-6b9d14cf88 (blob), ed-sandbox-public-6b9d14cf88 (blob)
  - sunbirdstagingpublic (legacy/staging, 19 public containers, the bulk of the 39): chatbot, dial, player, public, reports, samiksha, schema, sourcing, sunbird-content-dev, sunbird-content-staging, sunbird-content-staging-inquiry, sunbird-content-staging-knowlg, sunbird-dial-staging, sunbird-dial-staging-knowlg, termsandcondtions, uci, dbrestorescripts, privacy-policy, staging-offlineinstaller/sunbird-staging-offlineinstaller
  - The CSPM report's 39 likely covers additional subscriptions/accounts beyond what this az storage account list scope returned; the above is what's directly confirmed in this subscription.
- **Risk / why hold off:** Sunbird-Spark serves learner content through exactly these public blob URLs (SUNBIRD_CLOUD_STORAGE_URLS, public_container_name, used throughout player, knowlg, search). Flipping to Private **breaks content loading for every learner**. A Private Endpoint does **not** fix this; it only makes storage reachable from inside the VNet, and learners' browsers are on the public internet. The actual fix is Azure CDN/Front Door (or SAS-token URLs), *then* lock down direct blob access.
- **Cost:** Free to flip; CDN/Front Door adds a recurring cost that scales with bandwidth/requests. Needs a real traffic estimate to price properly.

#### Public network access disabled for storage accounts (6 resources) — `BLOCKER`

- **Why fix:** Same category as the deny-by-default rule; turns off public network reachability at the account level entirely.
- **Impact if fixed:** Storage account only reachable via Private Endpoint from inside the VNet.
- **Risk / why hold off:** Same two blockers as "Default network access rule" (reads *and* the direct-to-blob signed-URL upload path). If any of these 6 accounts serve public learner content, this breaks both until the CDN/upload-proxy work is done.
- **Cost:** Free to flip; the real fix has the same cost as above.

#### Storage account public access should be disallowed (6 resources) — `BLOCKER`

- **Why fix:** Account-level toggle that prevents any container on the account from ever being made public (stronger than the per-container setting).
- **Impact if fixed:** Locks out public access permanently at the account level, even for future containers.
- **Risk / why hold off:** Identical two blockers as above (reads *and* uploads) if any of these 6 accounts host learner-facing public content.
- **Cost:** Free to flip; the real fix has the same cost as above.

#### Storage accounts should restrict network access (4 resources) — `BLOCKER`

- **Why fix:** Same control family as "Default network access rule should be set to deny"; flags accounts with no network-level restriction configured at all.
- **Impact if fixed:** Only explicitly allowed networks/services can reach the storage account.
- **Risk / why hold off:** Identical two blockers: breaks public content reads and the direct-to-blob SAS-signed upload path.
- **Cost:** Free to flip; the real fix has the same cost as "Default network access rule" above.

## 4. What to Actually Do First

**1. Do now, no risk (20 controls)** · `SAFE`

All 9 Security Center Defender/pricing items, all 4 Storage soft-delete items, all 5 Compute hygiene items, both Monitor alerting items, and AKS logging.

**2. Already fixed in practice (1 control)** · `ALREADY FIXED`

Cognitive Services public access: networkAcls.defaultAction: Deny with only the 2 real cluster VNets allowed is already active. The CSPM tool still flags it because publicNetworkAccess is literally still "Enabled". Real exposure is closed; no action needed unless you want to chase the literal flag (would need Private Endpoints).

**3. Do soon, with prep (6 controls)** · `NEEDS PLANNING`

AKS authorized IPs (get CI/CD runner IPs first), AKS network policy (write NetworkPolicies before default-deny), SSH/management port restriction (get the team's real admin IPs first), NSG port audit.

**4. Do not touch without a separate project (7 controls)** · `BLOCKER`

The 6 Storage public-access controls (Default network deny, Blob Anonymous Access, Public access level for 39 containers, Public network access, Storage account public access, Storage accounts should restrict network access). These need a CDN/Front Door (or SAS-token) architecture change first, or they take the platform down. The Network HTTP(S) finding is a blocker for the same reason but is a **review, not a bulk flip**: the public ingress must stay open.