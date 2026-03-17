# Cloud Volumes ONTAP Fundamentals
> **NetApp Course** | STRSW-WBT-CVOTF | Duration: 1 hour | Format: Web-based training
> **Roles:** Administrator, engineer, architect, and operator
> **Prerequisites:** Introduction to NetApp Cloud Volumes ONTAP · Introduction to NetApp BlueXP · NetApp BlueXP Fundamentals

---

## Course Overview

This course provides a comprehensive introduction to **NetApp Cloud Volumes ONTAP (CVO)** — the cloud-native deployment of ONTAP software in public cloud environments. It covers CVO's architecture across AWS, Azure, and GCP; storage management features; data protection capabilities; and full integration with NetApp BlueXP for unified hybrid cloud management.

---

## Course Objectives

By the end of this course, you will be able to:

- Articulate the Cloud Volumes ONTAP-based storage system architectures
- Identify the various storage management options in Cloud Volumes ONTAP
- Discover how data is managed and protected in Cloud Volumes ONTAP
- Understand how Cloud Volumes ONTAP integrates with NetApp BlueXP

---

# LESSON 1 – Introduction

## 1.1 Cloud Volumes ONTAP Fundamentals

### What is Cloud Volumes ONTAP?

**NetApp Cloud Volumes ONTAP (CVO)** is the cloud-native deployment of NetApp's enterprise storage operating system — **ONTAP** — running as a software-defined storage solution on virtual machines in public cloud environments.

Unlike native cloud storage services (AWS EBS, Azure Managed Disks, GCP Persistent Disk), CVO brings the **full power of the ONTAP data management platform** to the cloud:

- The same ONTAP software running on-premises AFF/FAS systems runs inside CVO
- All ONTAP features are available: deduplication, compression, snapshots, SnapMirror, QoS, NVE encryption, multi-protocol (NFS/SMB/iSCSI), S3, FabricPool
- The same management tools (System Manager, CLI, REST API, BlueXP) work identically
- SnapMirror works natively between on-premises ONTAP and CVO — enabling true hybrid cloud architectures

### The CVO Value Proposition

| Challenge | CVO Solution |
|---|---|
| Enterprise apps need familiar NFS/SMB/iSCSI protocols in cloud | CVO provides all standard ONTAP protocols in cloud VMs |
| Migrating to cloud requires data format conversion | SnapMirror replicates data natively — no conversion |
| Cloud-native storage lacks enterprise data management features | CVO brings dedup, compression, snapshots, WORM, QoS |
| Managing on-prem and cloud storage requires separate tools | BlueXP manages both from a single interface |
| Cloud storage costs are unpredictable | FabricPool + efficiency features reduce storage costs |
| No ransomware protection in cloud-native storage | CVO includes ARP, SnapLock, immutable snapshots |

### CVO vs Native Cloud Storage Services

| Feature | AWS EBS / Azure Disk | Cloud Volumes ONTAP |
|---|---|---|
| **Storage protocols** | Block only (iSCSI-like) | NFS, SMB, iSCSI, S3 simultaneously |
| **Deduplication** | No | Yes (inline + post-process) |
| **Compression** | No | Yes (inline + adaptive) |
| **Snapshots** | Basic (1 per volume) | Up to 1,023 per volume; instant |
| **Replication** | Limited (same provider) | SnapMirror to any ONTAP (on-prem or cloud) |
| **WORM / Compliance** | No | SnapLock Compliance and Enterprise |
| **Ransomware protection** | No | ARP + immutable snapshots |
| **QoS** | Limited | Adaptive QoS per workload |
| **Multi-tenancy (SVMs)** | No | Yes (multiple SVMs per CVO) |
| **Management** | Per-cloud-provider console | BlueXP (unified across all clouds + on-prem) |
| **Data tiering** | Manual (application-level) | FabricPool (automatic, transparent) |

### CVO Supported Cloud Providers

| Cloud Provider | Service Name | GA Since |
|---|---|---|
| **Amazon Web Services (AWS)** | Cloud Volumes ONTAP for AWS | 2014 |
| **Microsoft Azure** | Cloud Volumes ONTAP for Azure | 2017 |
| **Google Cloud Platform (GCP)** | Cloud Volumes ONTAP for GCP | 2019 |

---

# LESSON 2 – NetApp Cloud Volumes ONTAP Architecture

## 2.1 CVO Architecture Overview

Cloud Volumes ONTAP runs ONTAP software inside **cloud provider virtual machines (VMs)**. The underlying cloud infrastructure (compute, networking, and raw storage) is provided by the cloud provider; the ONTAP software layer manages all storage services on top of that infrastructure.

### Deployment Options

CVO can be deployed in two fundamental configurations:

#### Single Node

```
┌─────────────────────────────────────────┐
│         Cloud Provider Region           │
│  ┌──────────────────────────────────┐   │
│  │   Availability Zone (AZ)         │   │
│  │                                  │   │
│  │  ┌──────────────────────────┐    │   │
│  │  │   CVO Instance (VM)      │    │   │
│  │  │   Running ONTAP software │    │   │
│  │  │                          │    │   │
│  │  │  - NFS/SMB/iSCSI/S3      │    │   │
│  │  │  - Dedup, Compression    │    │   │
│  │  │  - Snapshots             │    │   │
│  │  │  - FabricPool tiering    │    │   │
│  │  └──────────────────────────┘    │   │
│  │         │                        │   │
│  │  Cloud Storage Disks             │   │
│  │  (EBS / Managed Disks / PD)      │   │
│  └──────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

**Single node characteristics:**
- One cloud VM running ONTAP
- Suitable for: dev/test, non-critical workloads, cost-optimized deployments
- **No HA**: if the VM is terminated or the AZ fails, data is unavailable until VM restarts
- Lower cost (one VM, no data mirroring overhead)
- Faster deployment (~15 minutes)

#### HA Pair

```
┌──────────────────────────────────────────────────────────────────┐
│                    Cloud Provider Region                         │
│                                                                  │
│  ┌───────────────────────┐       ┌───────────────────────┐       │
│  │   Availability Zone 1 │       │   Availability Zone 2 │       │
│  │                       │       │                       │       │
│  │  ┌─────────────────┐  │       │  ┌─────────────────┐  │       │
│  │  │  CVO Node 1     │  │◄─────►│  │  CVO Node 2     │  │       │
│  │  │  (Active)       │  │ HA    │  │  (Mirror)       │  │       │
│  │  │  Serves I/O     │  │ sync  │  │  Standby        │  │       │
│  │  └─────────────────┘  │       │  └─────────────────┘  │       │
│  │         │             │       │         │             │       │
│  │  Local disks          │       │  Mirrored disks       │       │
│  └───────────────────────┘       └───────────────────────┘       │
│                                                                  │
│  Mediator instance (3rd AZ or same AZ — tiebreaker)              │
└──────────────────────────────────────────────────────────────────┘
```

**HA pair characteristics:**
- Two CVO VMs acting as an HA pair (same as on-premises ONTAP HA)
- Data is **synchronously mirrored** between the two nodes (NVRAM + storage)
- If Node 1 fails: Node 2 automatically takes over within ~60 seconds
- **Mediator**: a lightweight third instance (or VM in a third AZ) acts as tiebreaker
- Higher cost (two VMs + storage mirroring overhead)
- Recommended for **production workloads**

### CVO HA Across Availability Zones

| Provider | HA Deployment Options |
|---|---|
| **AWS** | Single AZ (both nodes same AZ) or Multi-AZ (nodes in different AZs) |
| **Azure** | Availability Set (same datacenter, different fault domains) |
| **GCP** | Zonal (same zone) or Regional (different zones) |

**Multi-AZ HA** provides the strongest availability guarantee — survives complete AZ failure — but adds network latency between nodes (typically <2ms within a region).

---

## 2.2 CVO Disk Types

CVO uses the **native cloud storage disks** provided by each cloud provider as its underlying storage medium. ONTAP manages these disks as part of its aggregate and RAID structure.

### AWS Disk Types

| Disk Type | AWS Name | Characteristics | Use Case |
|---|---|---|---|
| **gp3** | General Purpose SSD v3 | Configurable IOPS (3,000–16,000) and throughput (125–1,000 MB/s) independently of size | Default for most CVO workloads |
| **gp2** | General Purpose SSD v2 | IOPS scales with size (3 IOPS/GB); max 16,000 IOPS | Legacy (gp3 preferred) |
| **io1** | Provisioned IOPS SSD | Up to 64,000 IOPS; highest performance | Tier-1 databases, high-IOPS workloads |
| **st1** | Throughput Optimized HDD | High sequential throughput, low IOPS | Cold/warm data; FabricPool capacity tier |
| **sc1** | Cold HDD | Lowest cost; very low IOPS | Archive, very cold data |

**Key AWS storage concepts for CVO:**
- **EBS (Elastic Block Store)**: the AWS block storage service CVO uses for its disks
- EBS volumes are **network-attached** (not physically inside the VM) — they survive VM termination
- EBS supports **independent sizing** of capacity and performance (gp3)
- EBS volumes in an AZ are replicated internally by AWS for durability (99.999%)

### Azure Disk Types

| Disk Type | Azure Name | Max IOPS | Max Throughput | Use Case |
|---|---|---|---|---|
| **Premium SSD** | Premium Managed Disks | 20,000 IOPS/disk | 900 MB/s | Production performance workloads |
| **Premium SSD v2** | Premium SSD v2 | 80,000 IOPS/disk | 1,200 MB/s | High-performance databases |
| **Standard SSD** | Standard Managed Disks | 6,000 IOPS/disk | 750 MB/s | Dev/test, cost-optimized |
| **Standard HDD** | Standard HDD Managed Disks | 500 IOPS/disk | 60 MB/s | Cold/archive data |
| **Ultra Disk** | Azure Ultra Disk | 160,000 IOPS/disk | 2,000 MB/s | Mission-critical, extreme performance |
| **Azure Blob (Premium)** | Azure Blob Storage | N/A (object) | High throughput | FabricPool capacity tier |

### GCP Disk Types

| Disk Type | GCP Name | Max IOPS | Use Case |
|---|---|---|---|
| **pd-ssd** | SSD Persistent Disk | 100,000 IOPS | Performance tier |
| **pd-balanced** | Balanced Persistent Disk | 80,000 IOPS | Balanced cost/performance |
| **pd-standard** | Standard Persistent Disk | 7,500 IOPS | Cold/archive data |
| **pd-extreme** | Extreme Persistent Disk | 120,000 IOPS | Tier-1 databases |
| **GCS** | Google Cloud Storage | N/A (object) | FabricPool capacity tier |

### Disk Recommendations by Workload

| Workload Type | AWS | Azure | GCP |
|---|---|---|---|
| **General production** | gp3 | Premium SSD | pd-ssd or pd-balanced |
| **High-performance DB** | io1 | Premium SSD v2 or Ultra | pd-extreme |
| **Dev/test** | gp3 (smaller) | Standard SSD | pd-balanced |
| **FabricPool tier** | st1 or sc1 | Standard HDD | pd-standard |

---

## 2.3 CVO Storage Architecture

### How CVO Maps ONTAP Concepts to Cloud Infrastructure

CVO uses the same ONTAP storage hierarchy as on-premises systems, mapped onto cloud provider resources:

```
ONTAP LOGICAL LAYER              CLOUD PHYSICAL LAYER
────────────────────             ──────────────────────────────
RAID Groups              ←────── Groups of cloud disks (EBS, Managed Disks, PD)
Aggregates               ←────── Collections of RAID groups per node
Volumes (FlexVol)        ←────── Logical containers within aggregates
SVMs                     ←────── Logical tenant instances
LIFs                     ←────── Cloud ENI / NIC IPs (floating IPs for NAS)
NVRAM (write cache)      ←────── Memory in the cloud VM (+ NVRAM disk for HA)
```

### Aggregate Structure in CVO

In CVO, aggregates are built from cloud disks:

- **RAID-DP** is the default RAID type (same as on-premises) — 2 parity disks per RAID group
- ONTAP automatically creates RAID groups from the cloud disks assigned to an aggregate
- Recommended RAID group size: 5–20 disks depending on disk type and workload
- CVO supports up to **4 aggregates** per node in production configurations

**Example AWS aggregate:**
```
Aggregate: aggr1 (Node 1)
├── RAID Group 1:
│   ├── 5× gp3 disks (each 2 TiB)
│   ├── 1× gp3 parity disk (DP1)
│   └── 1× gp3 parity disk (DP2)
└── Usable capacity: 5 × 2 TiB = 10 TiB (minus RAID overhead)
```

### Core Disk (Root Aggregate)

Each CVO node has a dedicated **core disk** for the ONTAP root volume:
- Contains ONTAP system files, configuration, and logs
- Typically 1 disk (small size — 10–100 GB)
- Separate from data aggregates
- Never tiered or moved

### NVRAM Disk (HA Pair Only)

In HA configurations, CVO uses a dedicated **NVRAM disk** to persist the write cache:
- Simulates the hardware NVRAM found in physical ONTAP controllers
- Ensures write cache durability across VM restarts
- In AWS: one io1 disk per node dedicated to NVRAM simulation
- The HA partner mirrors NVRAM content over the HA interconnect for redundancy

### HA Interconnect and Cluster Network

In a CVO HA pair, two dedicated network paths exist:

| Network | Purpose | Protocol |
|---|---|---|
| **HA interconnect** | NVRAM mirroring, storage takeover coordination | iSCSI over cloud private network |
| **Cluster network** | ONTAP cluster communication, node coordination | Internal (same as on-prem cluster network) |

Both networks are implemented as cloud VM network interfaces (ENI in AWS, NIC in Azure/GCP) on **dedicated private subnets**.

### Floating IPs (NAS Data Access in HA)

For NAS (NFS/SMB) client access in an HA pair, CVO uses **floating IP addresses**:
- Each NAS LIF is assigned a floating IP from a dedicated CIDR range
- When a failover occurs, the floating IP migrates to the surviving node
- Cloud routing tables are updated automatically so clients always reach the active node
- This migration takes ~60 seconds and is transparent to NFS/SMB clients

**SAN access (iSCSI/FC):** Does not use floating IPs — uses static per-node IPs with host-side multipath (MPIO).

### Storage Capacity Limits

| Configuration | AWS (Single Node) | AWS (HA) | Azure (Single/HA) | GCP (Single/HA) |
|---|---|---|---|---|
| **Max raw capacity** | 368 TiB | 368 TiB per node | 368 TiB | 256 TiB |
| **Max usable (with efficiency)** | ~1 PiB+ | ~1 PiB+ | ~1 PiB+ | 800+ TiB |
| **Max volumes** | 500 | 500 | 500 | 500 |
| **Max SVMs** | 24 | 24 | 24 | 24 |

---

# LESSON 3 – NetApp Cloud Volumes ONTAP Features

## 3.1 Storage Efficiency Features

CVO inherits all of ONTAP's world-class storage efficiency technologies. In cloud environments, these features are especially valuable because they directly reduce cloud storage costs.

### Deduplication

**Inline deduplication** in CVO eliminates duplicate data blocks as they are written:
- Processes data in the write buffer before committing to cloud disks
- No I/O penalty on modern cloud VM instance types
- **Cross-volume deduplication**: deduplicates across all volumes in the same aggregate

**Typical savings by workload:**

| Workload | Typical Dedup Ratio |
|---|---|
| Virtual Desktop Infrastructure (VDI) | 3:1 – 10:1 |
| VMware vSphere workloads | 2:1 – 5:1 |
| Oracle / SQL Server databases | 1.2:1 – 3:1 |
| File shares (mixed content) | 1.5:1 – 3:1 |
| Backup data | 5:1 – 20:1 |

### Compression

CVO supports multiple compression modes:
- **Inline compression**: compresses data in the write buffer — zero additional latency on flash
- **Post-process compression**: background compression of already-stored data
- **Adaptive compression**: automatically selects 4 KB or 8 KB compression blocks based on data patterns
- **Temperature-sensitive storage efficiency (TSSE)**: aggressively compresses cold data as it ages

### Compaction

Packs multiple small data writes (< 4 KB) into single 4 KB physical blocks:
- Particularly effective for database workloads with small I/O
- Implemented transparently in the WAFL layer

### NetApp Guaranteed Storage Efficiency

NetApp guarantees **minimum 4:1 storage efficiency** for CVO AFF-equivalent deployments:
- Combination of dedup (2:1) + compression (2:1) = 4:1 baseline
- Real-world customer data frequently shows 5:1 to 10:1 ratios for VDI and virtual environments
- If the 4:1 ratio is not achieved, NetApp provides additional capacity credits

### Thin Provisioning

All CVO volumes are thin-provisioned by default:
- Volumes present more capacity to the host than is physically allocated on cloud disks
- Physical disk space is only consumed as data is actually written
- **Direct cost impact**: defer cloud disk purchases until capacity is actually needed
- Requires monitoring to prevent over-commitment (BlueXP alerts on capacity thresholds)

### FlexClone

CVO supports **FlexClone** — instant, space-efficient clones of volumes, files, or LUNs:
- Clone creation: seconds (metadata-only operation)
- Initial space consumption: zero (shared blocks with parent)
- Diverges as data is modified in the clone

**Cloud-specific FlexClone value:**
- Clone a 10 TB production database for dev/test: **zero additional cloud disk cost** initially
- Developers can work on isolated copies without duplicating storage expenses
- Refresh dev/test environments in seconds rather than hours

---

## 3.2 Multi-Protocol Support

CVO supports all major storage protocols simultaneously on the same system — a capability unique among cloud storage solutions:

### NAS Protocols

#### NFS (Network File System)
- Supported versions: **NFSv3, NFSv4, NFSv4.1**
- Used for: Linux/Unix workloads, container persistent volumes, HPC, analytics
- Access control via **export policies** (IP-based client lists with read/write/root permissions)
- **Kerberos authentication** supported for NFSv4 (SEC_KRB5/KRB5I/KRB5P)

**NFS in cloud context:**
- NFS LIFs use floating IPs (HA) or static IPs (single node)
- Cloud Security Groups / NSGs must allow TCP/UDP port 2049 from NFS clients
- Mount: `mount -t nfs <cvo-nfs-lif-ip>:/svm1_root/vol_name /mnt/mountpoint`

#### SMB/CIFS (Server Message Block)
- Supported versions: **SMB 2.0, 2.1, 3.0, 3.1.1**
- Used for: Windows workloads, home directories, file server consolidation
- Requires CIFS server joined to **Active Directory domain**
- Authentication: Kerberos (AD) or NTLM
- Access control: share-level permissions + NTFS file-level ACLs
- SMB 3.0 features: **encryption in transit**, multichannel, SMB Direct

**SMB in cloud context:**
- AD domain controllers must be reachable from the cloud VPC/VNet (via Direct Connect / ExpressRoute / VPN or cloud-hosted AD)
- Cloud Security Groups / NSGs must allow TCP 445 from SMB clients
- DNS resolution for CVO CIFS server name must work from client systems

### SAN Protocols

#### iSCSI
- Standard iSCSI over TCP/IP
- Used for: Windows SQL Server, Oracle on Linux, VMware VMFS datastores
- Hosts access CVO LUNs via software iSCSI initiator (built into Windows/Linux)
- **Multipath**: MPIO (Windows) or DM-Multipath (Linux) — uses two CVO node IPs for redundancy in HA
- Cloud port requirement: TCP 3260

#### NVMe/TCP (ONTAP 9.13+)
- NVMe protocol over standard TCP/IP networking
- Ultra-low latency compared to iSCSI
- Suited for high-performance database workloads requiring sub-millisecond latency

### Object Storage Protocol

#### S3 (ONTAP S3)
- CVO exposes an **S3-compatible object storage endpoint** directly
- Applications using standard S3 APIs (AWS SDK, boto3, etc.) can store data in CVO
- S3 buckets backed by FlexVol or FlexGroup volumes
- Multi-tenant S3 access via SVMs (each SVM = separate S3 namespace)
- **Use case**: cloud-native applications that use S3 for persistence but need ONTAP's data management features (snapshots, replication, encryption)

---

## 3.3 Multi-Tenancy with SVMs

CVO supports **multiple Storage Virtual Machines (SVMs)** per deployment, providing logical multi-tenancy:

- Each SVM has its own independent IP addresses, protocols, volumes, and access controls
- Multiple business units, applications, or customers can share a single CVO deployment
- Security isolation: compromise of one SVM cannot affect others
- Maximum **24 SVMs** per CVO HA pair

**Example multi-tenant CVO layout:**
```
CVO HA Pair (us-east-1)
├── SVM: svm_prod       → Production applications (NFS + iSCSI)
├── SVM: svm_dev        → Development and testing (NFS + SMB)
├── SVM: svm_analytics  → Data analytics workloads (NFS + S3)
└── SVM: svm_backup     → SnapVault backup destination (DP volumes)
```

---

## 3.4 Quality of Service (QoS)

CVO includes **adaptive QoS** policies to control and prioritize storage performance:

| QoS Type | Description | Use Case |
|---|---|---|
| **Max QoS (ceiling)** | Caps IOPS/throughput — prevents workloads from consuming all resources | Limit backup jobs; protect production from dev |
| **Min QoS (floor)** | Guarantees minimum performance — reserved even under load | Protect SLA for critical databases |
| **Adaptive QoS** | Auto-scales limits based on volume size (IOPS/TB) | VDI deployments with variable VM counts |

**Example QoS policy — protect production from backup:**
```bash
# Create a QoS ceiling for backup workload
qos policy-group create -policy-group backup_throttle \
  -vserver svm1 -max-throughput 200MB/s

# Apply to backup volume
volume modify -vserver svm1 -volume vol_backup \
  -qos-policy-group backup_throttle
```

---

## 3.5 FabricPool (Data Tiering in CVO)

**FabricPool** in CVO creates a two-tier storage architecture:
- **Performance tier**: cloud VM disks (EBS/Managed Disks/PD) — hot data
- **Capacity tier**: cloud object storage (S3/Blob/GCS) — cold data

**Cost impact example:**
```
Without FabricPool:
  100 TB total data on EBS gp3 → $0.08/GB/month = $8,000/month

With FabricPool (70% cold data):
  30 TB hot data on EBS gp3    → $2,400/month
  70 TB cold data on S3 Std-IA → $70 × $0.0125/GB/month = $875/month
  Total:                         $3,275/month (60% savings)
```

**Tiering policies available in CVO:**

| Policy | Behavior | Best For |
|---|---|---|
| **Snapshot-only** | Tier cold snapshot blocks only | Conservative; minimal latency risk |
| **Auto** | Tier cold active data + snapshot data | Most common production setting |
| **All** | Immediately tier all data to object store | Write-once archive; cold data ingestion |
| **None** | No tiering; all data stays on cloud disks | Latency-sensitive Tier-1 workloads |

---

## 3.6 Data Protection Features

### Snapshot Technology

CVO implements the full ONTAP **Snapshot** capability:
- Instantaneous point-in-time copies (milliseconds regardless of size)
- Up to **1,023 snapshots** per volume
- Configurable policies (hourly/daily/weekly schedules with retention counts)
- Self-service restore via `.snapshot` directory (NAS) or SnapRestore (SAN)
- **Zero space consumption** at creation (space only consumed as data changes)

### NetApp Volume Encryption (NVE)

CVO encrypts all data **at rest** using ONTAP Volume Encryption:
- **AES-256** encryption at the volume level
- Encryption keys managed by:
  - **ONTAP built-in key manager (OKM)**: keys stored within CVO itself
  - **External KMIP key manager**: HashiCorp Vault, Thales, Entrust — recommended for compliance
  - **Cloud provider KMS**: AWS KMS, Azure Key Vault, GCP Cloud KMS (via KMIP integration)
- Encryption is **transparent** to applications and hosts

**NVE in cloud context:**
Even though cloud providers encrypt EBS/Managed Disks by default, NVE provides an **additional layer** under NetApp's exclusive key management — ensuring that even cloud provider employees cannot access data.

### SnapLock (WORM Storage)

CVO supports **SnapLock** for immutable, WORM-compliant storage:

| Mode | Admin Can Delete? | Use Case |
|---|---|---|
| **Compliance** | No — not even root/admin | SEC 17a-4, FINRA, HIPAA strict compliance |
| **Enterprise** | Yes (with privileged delete + audit) | Ransomware protection, internal governance |

### Autonomous Ransomware Protection (ARP)

CVO includes ONTAP's **AI-powered ransomware detection**:
- Monitors I/O patterns on each volume (file extension changes, entropy, rename rates)
- **Learning phase**: 30-day behavioral baseline establishment
- **Active phase**: continuous anomaly detection
- On detection: auto-creates immutable snapshot + sends alert via BlueXP
- Cloud-specific benefit: ARP-generated snapshots protect against ransomware that has compromised cloud credentials — snapshots are locked and cannot be deleted even by cloud admin accounts

### Multi-Admin Verification (MAV)

Sensitive CVO operations (delete snapshots, break SnapMirror, disable ARP) require **approval from a second administrator**:
```bash
# Enable MAV on CVO
security multi-admin-verify modify -approval-groups storage_admins -required-approvers 1 -enabled true

# Protect snapshot deletion
security multi-admin-verify rule create -operation "volume snapshot delete"
```

---

## 3.7 High Availability and Non-Disruptive Operations

### HA Takeover and Giveback

CVO HA behavior mirrors on-premises ONTAP:

**Automatic takeover triggers:**
- VM termination or crash
- Network failure (cloud NIC failure)
- Cloud hardware failure causing VM unavailability
- AZ failure (Multi-AZ HA deployments)

**Takeover timeline (cloud):**
```
T=0:   Node 1 becomes unresponsive
T=15s: Node 2 detects missed heartbeats via HA interconnect
T=20s: Mediator confirms Node 1 is down (not a network partition)
T=30s: Node 2 begins takeover: mounts Node 1's aggregates
T=60s: Floating IPs migrated to Node 2; cloud routing updated
T=60s: Client reconnection; I/O resumes on Node 2
```

### Non-Disruptive Volume Move

Volumes can be moved between aggregates (e.g., to rebalance capacity or change disk types) without interrupting client access:
```bash
volume move start -vserver svm1 -volume vol_data \
  -destination-aggregate aggr2 -foreground false
```
CVO handles the cutover automatically — clients experience <1 second of I/O pause during the final cutover step.

### Instance Type Changes

Changing CVO to a larger or smaller VM instance type (to adjust compute performance or cost) requires a brief planned downtime:
- BlueXP orchestrates the instance change
- Data is preserved (cloud disks are reattached to the new instance)
- Typical process: ~15 minutes planned maintenance window

---

# LESSON 4 – Managing CVO with BlueXP

## 4.1 Cloud Volumes ONTAP with NetApp BlueXP

BlueXP is the **primary management interface** for Cloud Volumes ONTAP. While ONTAP System Manager and CLI are accessible directly (via the CVO management IP), BlueXP provides:

- **Deployment automation**: deploy new CVO environments without manual cloud console work
- **Unified operations**: manage CVO alongside on-premises ONTAP and other cloud storage from one interface
- **Integrated services**: backup, DR, tiering, ransomware protection activated with a few clicks
- **Health intelligence**: Digital Advisor recommendations surfaced directly in the CVO view
- **Cost visibility**: Digital Wallet tracks license consumption and spend

### Accessing CVO via BlueXP

```
BlueXP Canvas → Click CVO working environment icon
    │
    ├── Overview tab: capacity, performance, HA status, recent events
    ├── Volumes tab: list all volumes; create, resize, delete, configure protection
    ├── Aggregates tab: disk layout, capacity, RAID status, add disks
    ├── Replication tab: active SnapMirror relationships
    ├── Protection tab: snapshot policies, backup status, ARP status
    └── Settings tab: instance type, license, network, features
```

### Deploying a New CVO from BlueXP

**Step-by-step deployment wizard:**

```
1. Canvas → Add Working Environment → Amazon Web Services (or Azure / GCP)
2. Select: Single Node or HA Pair
3. Credentials: select AWS/Azure/GCP credentials (IAM role or service account)
4. Location:
   └── AWS: Region, AZ(s), VPC, Subnet(s)
   └── Azure: Region, Resource Group, VNet, Subnet
   └── GCP: Region, Zone(s), VPC, Subnet
5. Connectivity:
   └── Security Groups / NSGs (BlueXP can auto-create or use existing)
   └── Key pair (SSH) for emergency CLI access
6. License & Support Package:
   └── BYOL (serial number) or Pay-As-You-Go (cloud marketplace)
   └── Support package: Freemium, Professional, Essentials, Optimized
7. Pre-configured packages (simplified):
   └── Select workload type (Production, Dev/Test, Storage-only)
   └── BlueXP pre-selects optimal instance type and disk configuration
   OR
   Custom configuration (advanced):
   └── Select instance type manually
   └── Choose cloud disk type and size
   └── Set initial aggregate configuration
8. SVM name and NFS/CIFS configuration (optional at deploy time)
9. Review and Deploy
   └── BlueXP provisions all cloud resources and configures ONTAP (~20-30 minutes)
```

### CVO Instance Types by Cloud

**AWS — recommended EC2 instance families:**

| Instance Family | vCPU | RAM | Network | Use Case |
|---|---|---|---|---|
| **c5.2xlarge** | 8 | 16 GB | Up to 10 Gbps | Dev/test, small workloads |
| **c5.4xlarge** | 16 | 32 GB | Up to 10 Gbps | Medium workloads |
| **c5.9xlarge** | 36 | 72 GB | 10 Gbps | Large production workloads |
| **c5.18xlarge** | 72 | 144 GB | 25 Gbps | Maximum performance |
| **m5.8xlarge** | 32 | 128 GB | 10 Gbps | Memory-intensive workloads |
| **r5.2xlarge** | 8 | 64 GB | Up to 10 Gbps | In-memory database workloads |

**Azure — recommended VM families:**

| VM Size | vCPU | RAM | Network | Use Case |
|---|---|---|---|---|
| **DS4_v2** | 8 | 28 GB | Moderate | Dev/test |
| **DS13_v2** | 8 | 56 GB | Moderate | Production |
| **DS5_v2** | 16 | 56 GB | Extreme | High-performance |
| **E32s_v3** | 32 | 256 GB | Accelerated | Memory-intensive |
| **L8s_v2** | 8 | 64 GB | Moderate | NVMe-local storage |

**GCP — recommended machine families:**

| Machine Type | vCPU | RAM | Network | Use Case |
|---|---|---|---|---|
| **n1-standard-8** | 8 | 30 GB | 8 Gbps | General production |
| **n1-highmem-8** | 8 | 52 GB | 8 Gbps | Memory-intensive |
| **n2-standard-16** | 16 | 64 GB | 32 Gbps | High-performance |
| **n2-standard-32** | 32 | 128 GB | 32 Gbps | Maximum performance |

---

## 4.2 Protection: BlueXP Replication

**BlueXP Replication** manages SnapMirror relationships involving CVO, enabling:

### On-Premises to CVO (Cloud DR)

```
On-premises ONTAP                    Cloud Volumes ONTAP
────────────────────                 ─────────────────────────────
Production SVM                       DR SVM
  Source Volume          ──SM──►     Destination Volume (read-only DP)
  (AFF/FAS cluster)   async/sync     (AWS / Azure / GCP)
```

**Use case**: primary DR target is CVO — on failover, break SnapMirror on CVO and activate as production

### CVO to CVO (Cross-Region DR)

```
CVO us-east-1 (Primary)    ──SM──►    CVO eu-west-1 (DR)
```

**Use case**: geographic DR across cloud regions or providers

### CVO to On-Premises (Cloud-First with On-Prem DR)

```
CVO (Primary in cloud)     ──SM──►    On-premises ONTAP (DR)
```

**Use case**: cloud-native workload with on-premises fallback

### Creating a Replication from BlueXP Canvas

```
Canvas: Drag "Replication" connector line from source to destination
  └── Select: source SVM and volume(s)
  └── Select: destination SVM (create new or existing)
  └── Select: policy (Mirror, Mirror and Vault, Vault only)
  └── Select: max transfer rate (throttle if needed)
  └── Select: replication schedule
  └── Review and Initialize
```

---

## 4.3 Protection: BlueXP Ransomware Protection

**BlueXP Ransomware Protection** for CVO provides a unified policy-based interface to configure and monitor ransomware defenses across all CVO-hosted workloads.

### CVO-Specific Ransomware Protection Stack

```
LAYER 1: Prevention
  └── NVE encryption (data at rest)
  └── SMB signing + encryption (data in transit)
  └── RBAC + MFA for CVO administration
  └── Cloud Security Groups (network perimeter)

LAYER 2: Detection
  └── ARP (Autonomous Ransomware Protection)
       └── AI behavioral monitoring per volume
       └── Triggers on: new file extensions, high entropy, mass renames

LAYER 3: Response
  └── ARP auto-creates immutable snapshot on detection
  └── BlueXP Ransomware Protection receives alert
  └── Admin reviews incident in BlueXP dashboard
  └── Options: confirm attack → initiate recovery, or mark as false positive

LAYER 4: Recovery
  └── Restore from ARP-generated snapshot (local — minutes)
  └── Restore from SnapVault backup (secondary cluster — hours)
  └── Restore from BlueXP Backup cloud copy with DataLock (cloud — hours)
```

### BlueXP Ransomware Dashboard for CVO Workloads

```
BlueXP → Ransomware Protection → Dashboard
  ├── CVO workloads with protection status
  ├── % of CVO volumes with ARP enabled
  ├── % of CVO volumes with backup enabled
  ├── Recent incidents (ARP detections per CVO environment)
  └── Recovery readiness score per CVO deployment
```

---

## 4.4 Protection: BlueXP Disaster Recovery

**BlueXP Disaster Recovery** automates failover for **VMware workloads** running on CVO-backed datastores:

### CVO as VMware DR Target in Cloud

```
On-premises VMware (vCenter A)       Cloud (AWS/Azure/GCP)
───────────────────────────────      ──────────────────────────────
  ESXi hosts                           VMware Cloud / Cloud ESXi hosts
  VMs on ONTAP NFS datastores          NFS datastores on CVO
  ONTAP Cluster A              ──SM──► CVO (us-east-1 or eu-west-1)

BlueXP DR Service:
  ├── Discovers VMs and storage mapping
  ├── Creates DR Plan (boot order, network mapping)
  ├── Tests DR non-disruptively (FlexClone on CVO)
  └── Executes failover: break SM on CVO → register VMs → power on
```

### CVO DR Plan Parameters

| Parameter | Description |
|---|---|
| **Protected site** | On-premises vCenter with ONTAP-backed NFS datastores |
| **Recovery site** | Cloud vCenter/VMC cluster with CVO-backed NFS datastores |
| **Replication** | SnapMirror Async from on-prem ONTAP to CVO |
| **Test frequency** | Configurable non-disruptive test (uses FlexClone on CVO) |
| **Boot groups** | Infrastructure VMs boot first; application VMs boot in sequence |
| **Network mapping** | On-prem VLAN → Cloud VNet/VPC subnet mapping |

---

## 4.5 Protection: BlueXP Backup and Recovery

**BlueXP Backup and Recovery** for CVO sends volume backups to cloud object storage for long-term, cost-efficient protection independent of the CVO deployment itself.

### CVO Backup Architecture

```
Cloud Volumes ONTAP (CVO)
  └── Volume: vol_app_data (500 GB, changing 5% daily)
        │
        └── BlueXP Backup and Recovery
              │
              ├── Initial backup: 500 GB → S3/Blob/GCS (compressed + deduped)
              │                   → ~200 GB actual cloud storage (60% efficiency)
              └── Daily incremental: ~25 GB data change → ~10 GB cloud storage
```

### Backup Policies for CVO

BlueXP Backup policies are identical whether the source is on-prem ONTAP or CVO:

```
Policy: "cvo-production-backup"
  ├── Hourly:  keep 24 (last 24 hours)
  ├── Daily:   keep 30 (last 30 days)
  ├── Weekly:  keep 13 (last 13 weeks)
  ├── Monthly: keep 12 (last 12 months)
  └── Yearly:  keep 7  (last 7 years — compliance)

Storage tiering within backup:
  ├── 0-30 days:   S3 Standard / Azure Hot / GCS Standard
  ├── 31-90 days:  S3 Standard-IA / Azure Cool / GCS Nearline
  └── 91+ days:    S3 Glacier / Azure Archive / GCS Coldline

DataLock: Enabled (Governance) — prevents deletion before retention expiry
```

### Cross-Region and Cross-Account Backup

CVO backups can be stored in a **different region or different cloud account** than the CVO deployment:
- Protects against: regional outage, cloud account compromise, accidental CVO deletion
- Example: CVO in `us-east-1` → backups in `us-west-2` or `eu-west-1`
- Configured in BlueXP Backup policy destination settings

### Restore from CVO Backup

| Restore Scenario | Method | Time |
|---|---|---|
| Restore deleted file from 2 days ago | File browse → select file → restore to CVO | 1–5 minutes |
| Restore entire volume after ransomware | Volume restore from last clean backup | 30 min – 4 hours |
| Restore to different CVO in another region | Cross-region volume restore | 1–8 hours |
| Rapid access to backup data | Quick restore (FlexClone from backup) | 2–5 minutes (full copy in background) |

---

## 4.6 Governance: BlueXP Classification

**BlueXP Classification** scans CVO volumes to identify sensitive data, support compliance initiatives, and optimize storage costs.

### Scanning CVO with BlueXP Classification

```
BlueXP → Governance → Classification → Add Data Source → CVO
  └── Select working environment (CVO instance)
  └── Select SVMs and volumes to scan
  └── Start discovery scan
  └── Results available within hours (depending on data volume)
```

### What Classification Finds on CVO

**Sensitive data examples commonly found on NAS shares:**
- Customer PII stored in unstructured documents (Word files, Excel sheets)
- Source code repositories with API keys or credentials embedded
- Healthcare records (HIPAA-regulated PHI) in CSV or database exports
- Financial data in spreadsheets accessible to overly broad user groups

**Data optimization for CVO cost savings:**
- Stale data (not accessed in >1 year) that could be tiered via FabricPool or deleted
- Duplicate files consuming expensive cloud disk space
- Old log files and temp files that can be safely archived

---

## 4.7 Governance: BlueXP Digital Wallet

**BlueXP Digital Wallet** manages all CVO licensing in a single interface:

### CVO License Models

#### BYOL (Bring Your Own License)
- Purchase a capacity-based license from NetApp
- License is measured in **total TiB** provisioned across all CVO instances
- Register in Digital Wallet with license serial number
- One BYOL license can cover multiple CVO instances across clouds

**BYOL license management in Digital Wallet:**
```
BlueXP → Digital Wallet → Cloud Volumes ONTAP → BYOL Licenses
  ├── License: CVO-BYOL-2024-001
  │   ├── Capacity: 500 TiB total
  │   ├── Consumed: 327 TiB (65%)
  │   ├── Available: 173 TiB
  │   └── Expiration: 2026-12-31 → Alert sent 90/60/30 days before
  └── Add new license (enter serial number)
```

#### Pay-As-You-Go (PAYG) / Cloud Marketplace
- Subscribe via AWS Marketplace, Azure Marketplace, or GCP Marketplace
- Pay per GiB per hour based on actual consumption
- No upfront commitment — ideal for variable or unpredictable workloads
- Automatically billed to cloud account

#### CVO Essentials (Capacity-based)
- Flexible capacity pool that covers multiple CVO instances
- Annual subscription with option to purchase additional capacity
- Managed via Digital Wallet

#### Freemium
- Free tier: up to **500 GiB** of capacity per CVO instance
- Full ONTAP feature set included
- No time limit — useful for development, testing, and evaluation
- Upgrade to paid license when capacity exceeds 500 GiB

### License Recommendations from Digital Wallet

Digital Wallet provides proactive guidance:
- Alert when BYOL capacity utilization exceeds 80%
- Alert when license expires within 90/60/30 days
- **Optimization recommendation**: if PAYG cost is consistently higher than BYOL equivalent, Digital Wallet suggests switching to BYOL

---

## 4.8 Mobility: BlueXP Copy and Sync

**BlueXP Copy and Sync** enables data migration to and from CVO:

### CVO Migration Scenarios

#### Migrating from On-Premises NAS to CVO
```
Source: On-premises NFS/SMB share (NetApp or third-party)
Destination: CVO NFS/SMB volume
Method: BlueXP Copy and Sync (continuous sync until cutover)

Workflow:
1. Deploy Data Broker in cloud (EC2/Azure VM) or on-prem
2. Create sync relationship: on-prem NFS → CVO NFS volume
3. Run initial sync (copies all data — may take hours/days)
4. Enable continuous sync (near-real-time delta sync)
5. Cutover: update DNS/mount points; disable old share
```

#### Migrating Between CVO Instances (Cross-Region)
```
Source: CVO us-east-1 NFS volume
Destination: CVO eu-west-1 NFS volume
Use case: move workload to different cloud region for latency optimization
```

#### Cloud-to-Cloud Migration
```
Source: AWS S3 bucket
Destination: CVO ONTAP S3 (on-prem CVO via SnapMirror)
Use case: bring cloud-native object data under ONTAP management
```

### Copy and Sync Features Relevant to CVO

- **ACL preservation**: migrate Windows NTFS permissions from on-prem SMB to CVO SMB
- **Metadata preservation**: timestamps, ownership retained during migration
- **Bandwidth throttling**: limit sync throughput to avoid impacting production workloads
- **Continuous sync**: keep CVO destination in sync with source during phased migration

---

## 4.9 Mobility: BlueXP Tiering

**BlueXP Tiering** configures and monitors **FabricPool** tiering for CVO — automatically moving cold data from expensive cloud disks to lower-cost cloud object storage.

### CVO Tiering Setup

```
BlueXP → Mobility → Tiering → [Select CVO working environment]
  ├── View cold data analysis: how much data is cold on each aggregate?
  ├── Estimated monthly savings: $X/month if tiering enabled
  └── Set up tiering:
        ├── Attach object store (S3 bucket / Blob container / GCS bucket)
        ├── Select tiering policy per volume
        └── Set cooling period (days before data is considered cold)
```

### Cost Optimization with CVO Tiering

**Example scenario:**

| Configuration | Monthly Cost |
|---|---|
| 100 TiB CVO on gp3 EBS (no tiering) | $8,000/month |
| 100 TiB CVO: 40% hot (gp3) + 60% cold (S3-IA) | $3,875/month |
| **Savings** | **$4,125/month (52% reduction)** |

### Tiering Best Practices for CVO

- Use **snapshot-only** tiering first — lowest risk, immediate savings from snapshot cold blocks
- Enable **Auto** tiering for volumes with clear hot/cold data patterns
- Avoid tiering for latency-sensitive workloads (databases with random I/O patterns)
- Monitor **retrieval rates** — high retrieval rates indicate the cooling period is too aggressive
- Use **S3-IA / Azure Cool / GCS Nearline** for actively tiered data; **Glacier / Archive** for long-term archival

---

## 4.10 Health: BlueXP Digital Advisor

**BlueXP Digital Advisor** provides AI-driven health monitoring and recommendations specifically for CVO deployments.

### CVO-Specific Digital Advisor Insights

#### Wellness Score for CVO
Digital Advisor tracks the same risk categories for CVO as for on-premises ONTAP:

| Risk Category | CVO-Specific Examples |
|---|---|
| **Availability** | CVO HA pair not configured; Mediator unreachable; node restart events |
| **Performance** | Cloud instance type undersized for workload; aggregate overloaded; high I/O latency |
| **Capacity** | CVO aggregate >80% full; BYOL license capacity nearing limit |
| **Security** | Volumes without NVE; admin accounts without MFA; open security groups |
| **Configuration** | ONTAP version with known bugs; deviation from CVO best practices |

#### CVO-Specific Recommendations

| Recommendation | Description |
|---|---|
| **Enable FabricPool tiering** | Digital Advisor identifies cold data and estimates tiering savings |
| **Right-size instance type** | If CVO instance is over/under-provisioned relative to actual workload |
| **Enable ARP** | Volumes without ransomware protection flagged with remediation steps |
| **Update ONTAP** | If CVO runs an ONTAP version with known bugs fixed in a newer release |
| **Configure HA** | Single-node CVO with production workloads flagged as availability risk |

#### Upgrade Advisor for CVO

Digital Advisor recommends the optimal ONTAP version for CVO:
- Considers bug fixes relevant to the specific CVO deployment's configuration
- Provides non-disruptive upgrade guidance (CVO upgrades performed via BlueXP)
- CVO ONTAP upgrades are fully automated via BlueXP — no manual SSH required:
  ```
  BlueXP → [CVO working environment] → Settings → System Updates → Update
    └── BlueXP downloads image, performs pre-checks, and executes rolling upgrade
  ```

---

# Summary – Conceptual Map

```
CLOUD VOLUMES ONTAP FUNDAMENTALS
│
├── 🌐 INTRODUCTION
│   ├── CVO = ONTAP software running as cloud VMs (AWS/Azure/GCP)
│   ├── Full ONTAP feature set: dedup · compression · snapshots · SnapMirror · QoS · NVE
│   └── Same tools: System Manager · CLI · REST API · BlueXP
│
├── 🏗️ ARCHITECTURE
│   ├── Single Node: 1 VM, no HA, dev/test, lower cost
│   ├── HA Pair: 2 VMs, sync NVRAM mirror, Mediator, ~60s failover
│   │   └── Single-AZ or Multi-AZ (survives AZ failure)
│   │
│   ├── Disk Types by Cloud:
│   │   ├── AWS:   gp3 (default) · io1 (high IOPS) · st1/sc1 (FabricPool)
│   │   ├── Azure: Premium SSD · Premium SSD v2 · Ultra Disk · Standard HDD
│   │   └── GCP:   pd-ssd · pd-balanced · pd-extreme · pd-standard
│   │
│   ├── Storage Architecture:
│   │   ├── RAID-DP on cloud disks → Aggregates → FlexVol/FlexGroup Volumes
│   │   ├── Core disk (root) + NVRAM disk (HA) + Data aggregates
│   │   └── Floating IPs for NAS HA failover (routing table update in cloud)
│   │
│   └── Capacity: up to 368 TiB raw per node; 24 SVMs; 500 volumes
│
├── ⚡ FEATURES
│   ├── Storage Efficiency: dedup · compression · compaction · thin prov. · FlexClone
│   │   └── Guaranteed 4:1 efficiency ratio (real-world: 5:1–10:1 for VDI/VM)
│   ├── Multi-Protocol: NFS v3/4/4.1 · SMB 2/3 · iSCSI · NVMe/TCP · ONTAP S3
│   ├── Multi-Tenancy: up to 24 SVMs per CVO HA pair
│   ├── QoS: max ceiling · min floor · adaptive (per workload, per volume)
│   ├── FabricPool: hot tier (cloud disks) + cold tier (S3/Blob/GCS); 4 policies
│   └── Data Protection:
│       ├── Snapshots (1,023/vol; instant; self-service restore)
│       ├── NVE (AES-256; OKM or cloud KMS integration)
│       ├── SnapLock (WORM; Compliance or Enterprise)
│       ├── ARP (AI anomaly detection; auto immutable snapshot)
│       └── MAV (dual-admin approval for destructive operations)
│
└── ☁️ MANAGING CVO WITH BLUEXP
    ├── Deploy: wizard-based; auto-provisions all cloud resources
    ├── Replication: on-prem→CVO · CVO→CVO · CVO→on-prem (all SnapMirror types)
    ├── Ransomware Protection: unified policy; 4-layer defense; incident response
    ├── Disaster Recovery: VMware workload DR; DR plans; test/planned/unplanned failover
    ├── Backup and Recovery: incremental-forever to S3/Blob/GCS; DataLock; cross-region
    ├── Classification: scan CVO volumes; 130+ data types; GDPR/HIPAA compliance
    ├── Digital Wallet:
    │   ├── BYOL (capacity-based, multi-instance pool)
    │   ├── PAYG (cloud marketplace; per-GiB-hour)
    │   ├── Essentials (annual capacity subscription)
    │   └── Freemium (500 GiB free; full features)
    ├── Copy and Sync: migrate to/from CVO; continuous sync; ACL preservation
    ├── Tiering: FabricPool configuration from BlueXP; cold data analysis; cost estimation
    └── Digital Advisor: AI wellness score; CVO-specific risks; right-sizing; Upgrade Advisor
```

---

*Documentation generated from NetApp course STRSW-WBT-CVOTF – Cloud Volumes ONTAP Fundamentals*
*Extended detailed version – English*
