# ONTAP Data Protection Fundamentals
> **NetApp Course** | STRSW-WBT-DPFUND | Duration: 60 minutes | Format: Web-based training
> **Roles:** Backup administrators, data protection specialists, storage administrators
> **Prerequisite for:** ONTAP Data Protection Administration

---

## Course Overview

This course explores the full spectrum of NetApp ONTAP data protection capabilities — from foundational Snapshot technology to enterprise-grade disaster recovery, business continuity, backup/archive, and cloud-based protection. It covers planning considerations, solution comparison frameworks, and helps administrators identify the right ONTAP data protection solution for each business scenario.

---

## Course Objectives

By the end of this course, you will be able to:

- Evaluate the factors to consider when planning for data protection
- Identify the ONTAP data protection solutions that address business continuity, disaster recovery, and backup requirements
- Compare the various data protection solutions to determine which ones meet your organization's requirements

---

# MODULE 1 – ONTAP Data Protection Overview

## 1.1 ONTAP Data Protection Features

### Why Data Protection Matters

Organizations face an expanding set of data protection challenges that grow more critical each year:

| Threat Category | Examples | Business Impact |
|---|---|---|
| **Human error** | Accidental file deletion, misconfiguration, unintended overwrites | Data loss, compliance violation |
| **Hardware failure** | Drive failure, controller fault, power supply failure | Downtime, potential data loss |
| **Software corruption** | OS bugs, application errors, filesystem corruption | Data inconsistency, downtime |
| **Cybersecurity threats** | Ransomware, malware, insider threat | Mass data encryption/loss, extortion |
| **Site-level disaster** | Fire, flood, earthquake, power grid failure | Complete data center loss |
| **Regulatory requirements** | GDPR, HIPAA, SOX, PCI-DSS, SEC 17a-4 | Fines, legal liability if data not retained correctly |

### Data Protection Planning Factors

Before selecting a data protection solution, IT teams must evaluate:

**1. Recovery Point Objective (RPO)**
> *How much data can we afford to lose?*
- RPO = 0: zero data loss — every write must be confirmed on a secondary system
- RPO = minutes: periodic replication is acceptable
- RPO = hours: daily or nightly backups may be sufficient
- RPO = days: only for low-value, rarely-changed data

**2. Recovery Time Objective (RTO)**
> *How quickly must services be restored after a failure?*
- RTO = seconds: automatic failover with no human intervention
- RTO = minutes: manual failover with pre-staged DR environment
- RTO = hours: restore from backup, reconfigure hosts
- RTO = days: rebuild from scratch (unacceptable for mission-critical systems)

**3. Recovery Scope**
> *What needs to be recoverable?*
- Single file or directory
- Entire volume
- Entire SVM (all volumes + configuration)
- Entire storage system
- Entire data center site

**4. Data Consistency Requirements**
- **Crash-consistent**: data is in the state it would be if power was suddenly cut — acceptable for most OS and databases with journaling
- **Application-consistent**: the application is quiesced before the snapshot — required for databases (Oracle, SQL Server) and VMs
- **Replication-consistent**: all dependent systems are captured at the same point in time

**5. Retention Requirements**
- How many recovery points must be available?
- How far back in time must you be able to recover?
- Are immutable (WORM) copies required for compliance?
- Are offsite or air-gapped copies required?

**6. Cost and Infrastructure Constraints**
- Can secondary hardware be dedicated to protection, or must it be shared?
- Is cloud storage available as a backup target?
- What is the available network bandwidth for replication?
- What is the acceptable storage overhead for protection copies?

### ONTAP Data Protection Solution Categories

ONTAP organizes its data protection capabilities into three strategic categories aligned with business requirements:

```
┌─────────────────────────────────────────────────────────────────┐
│              ONTAP DATA PROTECTION PORTFOLIO                    │
│                                                                 │
│  BACKUP & ARCHIVE        DISASTER RECOVERY     BUSINESS         │
│  ─────────────────        ───────────────────  CONTINUITY       │
│  • Snapshots              • SnapMirror Async   ────────────     │
│  • SnapVault              • SnapMirror Sync    • SM Active Sync │
│  • SnapCenter             • SVM DR             • MetroCluster   │
│  • SnapMirror Cloud       • SnapMirror S3                       │
│  • BlueXP Backup                                                │
│                                                                 │
│  RPO: Hours–Days          RPO: Minutes–Zero    RPO: Zero        │
│  RTO: Hours               RTO: Minutes–Hours   RTO: Zero–Secs   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 1.2 ONTAP Snapshot Technology

### What is a Snapshot?

An ONTAP **Snapshot copy** is an instantaneous, read-only, point-in-time image of a volume. Snapshots are the fundamental building block of all ONTAP data protection — every other protection feature (SnapMirror, SnapVault, SnapCenter) ultimately relies on Snapshots as the unit of data transferred or retained.

### How Snapshots Work: Write Anywhere File Layout (WAFL)

ONTAP's internal filesystem, **WAFL (Write Anywhere File Layout)**, is specifically designed to make Snapshot creation instant and space-efficient.

**Key WAFL principle:** New data is **never written over existing data**. Every write goes to a new, previously unused block. This means:
- Creating a Snapshot is a metadata operation only (capture the current block map)
- No data needs to be copied when a Snapshot is taken
- Snapshot creation takes **milliseconds** regardless of volume size

**Copy-on-Write mechanics:**

```
STATE 1: Before any snapshot
─────────────────────────────────────────────────────
Active filesystem block map:  [A1][B1][C1][D1][E1]
Snapshot:                     (none)
Physical blocks:              [A1][B1][C1][D1][E1]

STATE 2: Snapshot taken (instant — zero space consumed)
─────────────────────────────────────────────────────
Active filesystem block map:  [A1][B1][C1][D1][E1]  ← same blocks
Snapshot block map:           [A1][B1][C1][D1][E1]  ← same blocks (shared)
Physical blocks:              [A1][B1][C1][D1][E1]  ← no duplication yet

STATE 3: Active filesystem modifies blocks A and C
─────────────────────────────────────────────────────
Active filesystem block map:  [A2][B1][C2][D1][E1]  ← new blocks A2, C2
Snapshot block map:           [A1][B1][C1][D1][E1]  ← still points to old blocks
Physical blocks:              [A1][B1][C1][D1][E1][A2][C2]  ← old + new coexist

SNAPSHOT SPACE = size of [A1] + [C1] (only the blocks that changed)
```

**Space efficiency:**
- A freshly created Snapshot consumes **zero additional space**
- Space is consumed only as the active data **diverges** from the snapshot state
- Snapshots of read-heavy workloads (databases, VMs) remain very small for a long time

### Snapshot Capabilities

| Capability | Detail |
|---|---|
| **Maximum snapshots per volume** | 1,023 |
| **Creation time** | Milliseconds (regardless of volume size) |
| **Minimum space consumed** | Zero (immediately after creation) |
| **Granularity** | Per-volume |
| **Access (NFS)** | Via hidden `.snapshot` directory at volume root |
| **Access (SMB)** | Via `~snapshot` or Windows "Previous Versions" |
| **Access (SAN)** | Via LUN clone or SnapRestore (no direct client access) |
| **Restore granularity** | Single file, LUN, or entire volume |

### Snapshot Policies and Scheduling

ONTAP allows fully automated Snapshot management through **Snapshot policies**:

```
Default Snapshot Policy (example):
├── Hourly:  keep 6 copies   (taken at :00 of each hour)
├── Daily:   keep 2 copies   (taken at 00:10 each night)
└── Weekly:  keep 2 copies   (taken Sunday at 00:15)
```

**Custom policies** can define:
- Any schedule (cron-style: minute, hour, day, day-of-week, month)
- Any retention count (1–1023 per schedule)
- Different policies for different volumes (production vs dev, databases vs file shares)

### Snapshot-Based Restore Operations

**Single file restore (NAS):**
- User navigates to `.snapshot/<snapshot_name>/` directory and copies the file back
- No admin involvement required — **end-user self-service**

**Single file restore (admin):**
```bash
volume snapshot restore-file -vserver svm1 -volume vol1 \
  -snapshot hourly.2025-09-01_1200 -path /data/reports/report.xlsx \
  -restore-path /data/reports/report_restored.xlsx
```

**Full volume revert:**
```bash
volume snapshot restore -vserver svm1 -volume vol1 \
  -snapshot daily.2025-08-31_0010
# WARNING: All changes after the snapshot are permanently discarded
```

**LUN restore from snapshot:**
```bash
# Create a LUN clone from a snapshot for verification, then overwrite if confirmed
lun copy start -vserver svm1 -path /vol/vol1/lun1_snap \
  -destination-vserver svm1 -destination-path /vol/vol1/lun1
```

---

## 1.3 Data Protection Relationships

ONTAP data protection is built around the concept of **relationships** — logical connections between a source (primary) and a destination (secondary) that define how and when data is replicated.

### Relationship Concepts

| Concept | Description |
|---|---|
| **Source volume** | The primary production volume being protected |
| **Destination volume** | The secondary copy (read-only DP volume during normal operation) |
| **Relationship type** | Defines the replication behavior (async, sync, vault, etc.) |
| **Policy** | Controls snapshot selection, retention on destination, and throttle |
| **Schedule** | When replication updates are triggered (for async) |
| **Lag time** | How far behind the destination is from the source |
| **Healthy status** | Whether the relationship is functioning normally |

### Cluster and SVM Peering

Before any SnapMirror relationship can be created between two clusters, the clusters must be **peered**:

**Cluster peering:**
```bash
# On source cluster:
cluster peer create -peer-addrs <destination-intercluster-IP> -ipspace Default

# On destination cluster:
cluster peer create -peer-addrs <source-intercluster-IP> -ipspace Default
```

**SVM peering** (required for cross-cluster volume relationships):
```bash
vserver peer create -vserver src_svm -peer-vserver dst_svm \
  -peer-cluster dst_cluster -applications snapmirror
```

### Common Relationship Types Summary

| Type | Technology | RPO | Direction |
|---|---|---|---|
| **XDP (Extended Data Protection)** | SnapMirror Async / SnapVault | Minutes–Hours | One-way |
| **DP (Data Protection)** | Legacy SnapMirror (being retired) | Minutes | One-way |
| **Sync** | SnapMirror Synchronous | Zero | One-way |
| **StrictSync** | SnapMirror Synchronous (strict) | Zero | One-way |
| **SM-BC / Active Sync** | SnapMirror Active Sync | Zero | Active-Active |

---

# MODULE 2 – Disaster Recovery Solutions

## 2.1 Disaster Recovery: Use Cases

**Disaster Recovery (DR)** focuses on restoring operations after an event that makes the primary system unavailable. DR solutions accept some data loss (RPO > 0) or recovery time (RTO > 0) in exchange for lower cost and complexity compared to business continuity solutions.

### When DR Solutions Are Appropriate

| Scenario | Appropriate DR Approach |
|---|---|
| Primary storage array failure | Failover to SnapMirror secondary on same site |
| Primary data center failure (planned) | Scheduled SnapMirror failover to remote site |
| Ransomware or mass data corruption | Failover to clean SnapMirror copy before infection |
| Regional disaster (flood, earthquake) | Geographic DR to remote region |
| SVM-level failure or corruption | SVM DR with SnapMirror for SVM |

### DR Planning Considerations

- **Distance between sites**: longer distance = higher latency = higher RPO for synchronous solutions
- **Bandwidth between sites**: limits how quickly replication can keep up with change rate
- **Data change rate**: high-change workloads generate more replication traffic
- **Consistency groups**: applications spanning multiple volumes need all volumes replicated together to a consistent point
- **Automation**: manual DR failover is error-prone; automation reduces RTO and human error

---

## 2.2 SnapMirror (Asynchronous)

**SnapMirror** asynchronous is the primary ONTAP disaster recovery and data replication technology. It replicates Snapshot copies from a source volume to a destination volume on a schedule.

### SnapMirror Architecture

```
SOURCE CLUSTER                    DESTINATION CLUSTER
──────────────────                ──────────────────────
Production SVM                    DR SVM
  └── Source Volume               └── Destination Volume (DP, read-only)
         │                                    │
         │   SnapMirror Relationship          │
         │◄──────────────────────────────────►│
         │                                    │
  Snapshots:                        Snapshots (replicated):
  - hourly.09-01_1200               - hourly.09-01_1200  ← common
  - hourly.09-01_1300               - hourly.09-01_1300  ← common
  - hourly.09-01_1400 (latest)      ← being transferred
```

### SnapMirror Replication Process

**Step 1 — Initialize (baseline transfer):**
- A full Snapshot of the source volume is transferred to the destination
- This is the only full copy transfer — all subsequent transfers are incremental
- Duration depends on data size and network bandwidth

**Step 2 — Incremental updates:**
- On schedule (e.g., every hour), ONTAP:
  1. Creates a new Snapshot on the source
  2. Identifies changed blocks between the new snapshot and the last common snapshot
  3. Transfers only the **changed blocks** to the destination
  4. Updates the destination volume to reflect the new snapshot
  5. Prunes old snapshots per the SnapMirror policy

**Step 3 — Failover (disaster occurs):**
```bash
# Break the SnapMirror relationship (destination becomes read-write)
snapmirror break -destination-path dst_svm:dst_vol

# Redirect hosts to destination volume
# (update DNS, remount NFS, rescan SAN)
```

**Step 4 — Resync (after primary is restored):**
```bash
# Resync: transfers only changed data back to source
snapmirror resync -source-path src_svm:src_vol \
  -destination-path dst_svm:dst_vol
```

### SnapMirror Policies

SnapMirror policies control which Snapshots are replicated and how many are retained on the destination.

**Built-in policies:**

| Policy | Type | Behavior |
|---|---|---|
| `MirrorAllSnapshots` | Async Mirror | Replicates all source snapshots to destination |
| `MirrorLatest` | Async Mirror | Replicates only the latest snapshot |
| `MirrorAndVault` | Async Mirror+Vault | Combines DR mirror with long-term vault retention |
| `Unified7year` | Vault | Retains snapshots for 7 years (compliance) |
| `XDPDefault` | Async XDP | Default for XDP relationships |

**Custom policy example — replicate hourly, keep 24 on destination:**
```bash
snapmirror policy create -policy custom_dr -type async-mirror
snapmirror policy add-rule -policy custom_dr \
  -snapmirror-label hourly -keep 24
snapmirror policy add-rule -policy custom_dr \
  -snapmirror-label daily -keep 30
```

### SnapMirror Throttling

Control bandwidth consumed by replication to avoid impacting production:
```bash
# Limit SnapMirror to 100 MB/s
snapmirror modify -destination-path dst_svm:dst_vol \
  -throttle 102400   # Value in KB/s
```

### SnapMirror Fan-Out and Cascade

**Fan-out:** One source replicates to multiple destinations (DR + backup simultaneously)
```
Source Volume ──→ DR Site (SnapMirror Async)
               └─→ Backup Site (SnapVault)
               └─→ Cloud (SnapMirror Cloud)
```

**Cascade:** Source replicates to intermediate, which replicates to final destination
```
Production ──→ DR Site ──→ Tape/Archive Site
```

---

## 2.3 SnapMirror for SVM DR

**SVM DR** extends SnapMirror to replicate not just volumes, but the **entire SVM configuration** — network interfaces, CIFS server, NFS exports, igroups, users, and all volumes.

### What SVM DR Replicates

| Component | Replicated? |
|---|---|
| All volumes (data) | ✅ Yes |
| Volume Snapshot copies | ✅ Yes |
| SVM network interfaces (LIFs) | ✅ Yes (IPs preserved or remapped) |
| CIFS server configuration | ✅ Yes |
| NFS export policies | ✅ Yes |
| iSCSI/FC igroups and LUN maps | ✅ Yes |
| Local users and groups | ✅ Yes |
| DNS and name service config | ✅ Yes |
| SVM-level Snapshot policies | ✅ Yes |
| Encryption keys (NVE) | ✅ Yes (if key manager peered) |

### SVM DR Failover

During failover, the destination SVM is **activated**:
```bash
# Activate the destination SVM (breaks relationship, brings SVM online)
snapmirror break -destination-path dst_cluster://dst_svm
vserver start -vserver dst_svm
```

Clients reconnect to the same IP addresses (if IPs were preserved) or updated DNS records.

### SVM DR Use Cases

- Complete SVM migration to a new cluster (non-disruptive migration)
- Full SVM recovery after corruption or accidental deletion
- DR for multi-protocol environments where re-creating configuration manually would take hours

---

## 2.4 SnapMirror Synchronous

**SnapMirror Synchronous** replicates every write to both the source and destination before acknowledging the I/O to the host application — guaranteeing **RPO = 0** (zero data loss).

### How Synchronous Replication Works

```
Host Application
      │
      ▼ Write I/O
Source ONTAP Node
      │
      ├── Write to source NVRAM
      │
      ├── Simultaneously send write to destination via SnapMirror Sync
      │         │
      │         ▼
      │   Destination ONTAP Node
      │         │
      │         └── Write to destination NVRAM
      │                   │
      │                   └── ACK back to source
      │
      └── Source sends ACK to host ← only after BOTH sides confirm

Result: Source and destination are ALWAYS in sync (RPO = 0)
```

### SnapMirror Synchronous Modes

| Mode | Behavior on Network Failure | RPO |
|---|---|---|
| **Sync** | Falls back to async mode; continues serving I/O | Near-zero (brief async window) |
| **StrictSync** | Halts I/O on source if destination becomes unreachable | Absolute zero — but risks I/O unavailability |

**Sync mode** is recommended for most production workloads — it maintains zero RPO under normal conditions but does not halt production if connectivity to the DR site is briefly interrupted.

**StrictSync** is reserved for workloads where data loss is absolutely unacceptable even at the cost of availability (e.g., financial transaction logs, medical records).

### SnapMirror Synchronous Requirements

| Requirement | Detail |
|---|---|
| **Network latency** | ≤10 ms RTT between source and destination (ideally ≤5 ms) |
| **Bandwidth** | Must accommodate the peak write rate of the source workload |
| **ONTAP version** | ONTAP 9.5+ for SnapMirror Synchronous |
| **License** | SnapMirror Synchronous license (included in ONTAP One) |
| **Supported volumes** | FlexVol only (not FlexGroup) |
| **Max relationships** | Up to 80 sync relationships per HA pair (ONTAP 9.9.1+) |

### SnapMirror Synchronous Resync

After a disruption, resync transfers only the diverged data:
```bash
snapmirror resync -destination-path dst_svm:dst_vol
```

---

## 2.5 SnapMirror S3

**SnapMirror S3** extends SnapMirror replication to **S3 buckets** — enabling replication of object storage data between ONTAP S3 buckets or between ONTAP and cloud S3 endpoints.

### SnapMirror S3 Use Cases

- **S3 bucket DR**: replicate an ONTAP S3 bucket to another ONTAP cluster for disaster recovery
- **S3 to cloud backup**: replicate ONTAP S3 data to AWS S3, Azure Blob, or GCP GCS for offsite backup
- **Cloud-native application protection**: protect applications that use S3 APIs for data storage

### SnapMirror S3 Architecture

```
Source ONTAP S3 Bucket          Destination
──────────────────────          ──────────────────────────────
  SVM with S3 service    ──→    ONTAP S3 Bucket (on another cluster)
                         ──→    AWS S3 Bucket
                         ──→    Azure Blob Storage
                         ──→    Google Cloud Storage
```

### Key Differences from Volume SnapMirror

| Feature | SnapMirror (Volumes) | SnapMirror S3 |
|---|---|---|
| Replication unit | Volume + Snapshots | S3 objects |
| Destination type | ONTAP volume (DP) | S3 bucket (ONTAP or cloud) |
| Protocol | ONTAP-native | S3 API |
| Consistency | Snapshot-based | Eventually consistent |
| Primary use | Volume DR, backup | Object storage replication |

---

# MODULE 3 – Business Continuity Solutions

## 3.1 Business Continuity: Use Cases

**Business Continuity (BC)** solutions go beyond disaster recovery: they ensure that applications **continue serving I/O without interruption** even during a failure event. BC solutions target:

- **RPO = 0**: zero data loss
- **RTO = 0 or near-zero**: no application downtime; I/O continues transparently

### When BC Solutions Are Required

| Scenario | Requirement |
|---|---|
| Financial trading platform | Even seconds of downtime = millions lost; zero RPO/RTO mandatory |
| Healthcare patient records | Real-time data must always be available; data loss unacceptable |
| Telecommunications core systems | Service unavailability affects millions of subscribers |
| E-commerce checkout during peak | Any downtime during Black Friday = major revenue loss |
| Manufacturing control systems | Plant shutdown from storage failure = enormous cost |

### BC vs DR: Key Differences

| Dimension | Disaster Recovery | Business Continuity |
|---|---|---|
| RPO | Minutes to zero | Zero |
| RTO | Minutes to hours | Zero to seconds |
| Application downtime | Yes (during failover) | No (transparent) |
| Cost | Lower | Higher |
| Complexity | Medium | High |
| Example technology | SnapMirror Async | SnapMirror Active Sync, MetroCluster |

---

## 3.2 SnapMirror Active Sync

**SnapMirror Active Sync** (formerly SnapMirror Business Continuity / SM-BC) allows **two ONTAP clusters to simultaneously serve I/O for the same dataset** — providing zero RPO and zero RTO for SAN workloads.

### SnapMirror Active Sync Architecture

```
Site A                              Site B
──────────────────────              ──────────────────────────
  ONTAP Cluster A                     ONTAP Cluster B
  SVM-A                               SVM-B
  └── Volume A (primary)              └── Volume A' (mirror)
      LUN /vol/vol_a/lun1                 LUN /vol/vol_a_mir/lun1

  Host A ──→ LUN (Site A)          Host B ──→ LUN (Site B)

Both hosts see the SAME logical LUN, served from their local site.
Writes are synchronously committed to BOTH sites before ACK.

        ↕ Synchronous mirroring via SnapMirror Active Sync ↕

ONTAP Mediator (third site or cloud VM)
  └── Breaks ties during network partition; triggers auto-failover
```

### How Active Sync Works

1. Both sites serve **read I/O locally** (from their own copy) — lowest possible read latency
2. **Write I/O** is synchronously committed to both sites before acknowledging the host
3. If Site A fails:
   - ONTAP Mediator detects the failure (Site A no longer responds to Mediator)
   - Site B is granted exclusive I/O rights
   - Site B continues serving all I/O — **hosts at Site B experience zero interruption**
   - Hosts at Site A may need to be restarted or reconnected (host-level consideration)

### SnapMirror Active Sync Capabilities

| Capability | Detail |
|---|---|
| **RPO** | Zero |
| **RTO** | Zero (for hosts at surviving site); seconds for failed-site hosts |
| **Protocol support** | iSCSI, FC, NVMe/FC (SAN only — not NAS) |
| **Supported volumes** | FlexVol only |
| **Host OS support** | VMware vSphere, Windows, Linux, AIX, Solaris |
| **VMware VAAI** | Supported (storage-side accelerated operations) |
| **ONTAP Mediator** | Required (provides tiebreaker for split-brain scenarios) |
| **Max distance** | Effectively unlimited (limited only by network latency meeting <10ms RTT) |
| **Consistency groups** | Multiple volumes protected as a single consistent unit |

### ONTAP Mediator

The **ONTAP Mediator** is a lightweight software service deployed on a third host (physical or VM) independent of both storage sites.

**Role:**
- Maintains a continuous communication channel with both ONTAP clusters
- Acts as a **tiebreaker** during split-brain scenarios (both sites are running but cannot communicate with each other)
- If a site fails, Mediator confirms the failure and allows the surviving site to fence the failed site and take exclusive I/O ownership

**Deployment:**
- Installed as a Linux application on a separate VM or physical server
- Must be reachable by both clusters but outside both primary sites (cloud VM is valid)
- High availability of the Mediator itself is recommended

### Consistency Groups (CGs)

For applications that span multiple volumes (e.g., an Oracle database with data, redo logs, and control files on separate volumes), all volumes must be protected and failed over together as a **consistency group**.

```bash
# Create a consistency group for multi-volume application
consistency-group create -vserver svm1 -consistency-group app_cg \
  -volumes vol_data,vol_logs,vol_control

# Apply SnapMirror Active Sync to the consistency group
snapmirror create -source-path svm1:/cg/app_cg \
  -destination-path svm2:/cg/app_cg_mirror \
  -policy AutomatedFailOver
```

---

## 3.3 MetroCluster

**MetroCluster** is NetApp's hardware-level business continuity solution providing **site-level failover with zero RPO and near-zero RTO** through synchronous hardware mirroring.

### MetroCluster Architecture Overview

MetroCluster operates at a fundamentally different level than SnapMirror — it mirrors at the **RAID/aggregate level**, meaning every write to every disk on Site A is synchronously mirrored to Site B at the storage hardware level.

```
SITE A                                    SITE B
──────────────────────                    ──────────────────────
  ONTAP Cluster A                           ONTAP Cluster B
  ┌───────────────┐                         ┌───────────────┐
  │  Node 1       │                         │  Node 3       │
  │  Node 2       │◄── Sync Mirror ────────►│  Node 4       │
  └───────────────┘  (hardware level)       └───────────────┘
         │                                          │
  Local disks +                             Local disks +
  Mirror copies from Site B                 Mirror copies from Site A
         │                                  │
  ┌──────┴──────────────────────────────────┴──────┐
  │           ISL (Inter-Switch Link)              │
  │        FC fabric or IP network                 │
  └────────────────────────────────────────────────┘
              ↕
        ONTAP Mediator (IP-based MetroCluster)
        or FC-VI adapter (FC-based MetroCluster)
```

### MetroCluster Configurations

| Configuration | Description | Max Distance | Connectivity |
|---|---|---|---|
| **MetroCluster FC (2-node)** | 2 nodes, FC fabric | Up to 300 km | Fibre Channel |
| **MetroCluster FC (4-node)** | 2 HA pairs (4 nodes total) | Up to 300 km | Fibre Channel |
| **MetroCluster FC (8-node)** | 4 HA pairs (8 nodes total) | Up to 300 km | Fibre Channel |
| **MetroCluster IP (2-node)** | 2 nodes, IP network | Up to 700 km (with DWDM) | IP / Ethernet |
| **MetroCluster IP (4-node)** | 2 HA pairs (4 nodes total) | Up to 700 km | IP / Ethernet |
| **MetroCluster IP (8-node)** | 4 HA pairs (8 nodes total) | Up to 700 km | IP / Ethernet |

### MetroCluster Key Components

| Component | Description |
|---|---|
| **Plexes** | Each aggregate has two copies (plexes) — one on each site |
| **SyncMirror** | The mirroring technology that keeps both plexes synchronized |
| **ISL (Inter-Switch Link)** | Dedicated high-bandwidth link between the two sites |
| **ONTAP Mediator** | Required for MetroCluster IP; provides automated switchover |
| **FC-VI adapters** | Required for MetroCluster FC; provide cluster interconnect over FC |

### MetroCluster Switchover and Switchback

**Negotiated switchover** (planned — e.g., for site maintenance):
```bash
# From surviving/healthy cluster:
metrocluster switchover
# Site B takes ownership of all workloads; Site A goes offline for maintenance
```

**Automatic (unplanned) switchover:**
- Site A completely fails (power loss, disaster)
- ONTAP Mediator confirms Site A is unreachable
- Site B automatically takes over all workloads within ~30 seconds
- No human intervention required

**Switchback** (after Site A is restored):
```bash
metrocluster heal -phase aggregates
metrocluster heal -phase root-aggregates
metrocluster switchback
# All workloads return to their home site
```

### MetroCluster vs SnapMirror Active Sync

| Dimension | MetroCluster | SnapMirror Active Sync |
|---|---|---|
| **Mirroring level** | Hardware (RAID/aggregate) | Software (volume) |
| **Protocol support** | NAS + SAN (all protocols) | SAN only (iSCSI, FC, NVMe/FC) |
| **RPO** | Zero | Zero |
| **RTO** | ~30 seconds (auto) | Near-zero |
| **Max distance** | 700 km (IP) | Network latency limited (<10ms) |
| **Scale** | Up to 8 nodes per config | Per HA pair |
| **Consistency** | Entire cluster | Consistency groups |
| **Cost** | Higher (dedicated HW) | Lower (software-based) |
| **Best for** | All-protocol enterprise DC | SAN-focused BC |

---

## 3.4 Comparing Synchronous Solutions

| Feature | SnapMirror Sync | SnapMirror Active Sync | MetroCluster |
|---|---|---|---|
| **RPO** | Zero | Zero | Zero |
| **RTO** | Minutes (manual failover) | Near-zero (automatic) | ~30 seconds |
| **Protocols** | NAS + SAN (volume-level) | SAN only | All protocols |
| **Application transparency** | No (requires failover) | Yes (transparent) | Yes (transparent) |
| **Active-Active I/O** | No (one site active) | Yes (both sites active) | Yes (both sites active) |
| **ONTAP Mediator required** | No | Yes | Yes (IP) |
| **Max volumes/LUNs** | 80 relationships/HA pair | 80 CGs, 16 vol/CG | Entire cluster |
| **Granularity** | Per volume | Per consistency group | Per cluster |
| **Typical deployment** | Single datacenter (short distance) | Dual datacenter SAN | Enterprise dual datacenter |
| **Licensing** | Included in ONTAP One | Included in ONTAP One | Separate MetroCluster license |

---

# MODULE 4 – Backup and Archive Solutions

## 4.1 Backup and Archive: Use Cases

While DR and BC solutions protect against site failures with low RPO/RTO, **backup and archive** solutions address different requirements:

| Requirement | Backup | Archive |
|---|---|---|
| **Purpose** | Restore data after loss or corruption | Retain data for compliance or historical access |
| **Retention** | Days to months (operational recovery) | Months to years (regulatory, legal) |
| **Access frequency** | Occasional (only when needed for recovery) | Rare (retrieval for audit or legal discovery) |
| **RPO** | Hours to days | Hours to days |
| **RTO** | Hours | Hours to days |
| **Immutability** | Recommended | Often required (WORM) |
| **Cost target** | Low-medium | Very low (cold storage) |

### Backup vs Snapshot vs Replication

| Feature | Snapshot | SnapMirror (DR) | Backup (SnapVault) |
|---|---|---|---|
| Location | Same system as source | Remote system (same software) | Separate system or cloud |
| Protects against hardware failure? | No (same storage) | Yes | Yes |
| Protects against ransomware? | Partial (if admin credentials compromised) | Partial | Yes (independent copy) |
| Retention duration | Hours to weeks | Days to weeks | Months to years |
| Restore granularity | File, LUN, volume | Volume, SVM | File, LUN, volume |
| Independence from source | No | Partial | Full |

---

## 4.2 SnapMirror for Backup and Archive

SnapMirror in **XDP (Extended Data Protection)** mode with a **vault policy** provides ONTAP's native disk-to-disk backup capability (formerly known as SnapVault).

### SnapVault Architecture

```
Production Cluster                    Backup/Vault Cluster
────────────────────                  ────────────────────────
Source SVM                            Vault SVM
  └── Production Volume               └── Vault Volume (DP, read-only)
      │                                   │
      │ Snapshots retained:               │ Snapshots retained (independently):
      │ - hourly × 6                      │ - daily  × 30
      │ - daily  × 2                      │ - weekly × 52
      │ - weekly × 2                      │ - monthly × 24
      │                                   │
      └──────────SnapMirror XDP──────────►│
                 (vault policy)
```

### Key SnapVault Behaviors

- **Source independence**: snapshots retained on the vault are **independent** of the source. Deleting a snapshot on the source does NOT delete it from the vault.
- **Long-term retention**: vault policies can retain snapshots for weeks, months, or years.
- **Efficient transfer**: only changed blocks are transferred (not full copies).
- **Vault-to-vault cascade**: a vault copy can itself be replicated to a second vault for additional protection.

### SnapVault Policy Configuration

```bash
# Create a vault policy with long-term retention rules
snapmirror policy create -policy vault_policy -type vault
snapmirror policy add-rule -policy vault_policy \
  -snapmirror-label daily -keep 30
snapmirror policy add-rule -policy vault_policy \
  -snapmirror-label weekly -keep 52
snapmirror policy add-rule -policy vault_policy \
  -snapmirror-label monthly -keep 24

# Create the SnapVault relationship
snapmirror create -source-path src_svm:src_vol \
  -destination-path vault_svm:vault_vol \
  -policy vault_policy -schedule daily
```

### Restoring from SnapVault

**Restore a single file from vault to production:**
```bash
# On the vault cluster — restore a specific file back to production
snapmirror restore -source-path vault_svm:vault_vol \
  -destination-path src_svm:src_vol \
  -source-snapshot weekly.2025-08-31_0015 \
  -file-list /data/finance/q2_report.xlsx
```

**Restore entire volume:**
```bash
snapmirror restore -source-path vault_svm:vault_vol \
  -destination-path src_svm:recovered_vol \
  -source-snapshot monthly.2025-08-01_0015
```

---

## 4.3 SnapCenter

**SnapCenter** is NetApp's enterprise backup and recovery platform — a centralized application-aware backup solution built on top of ONTAP Snapshot, SnapMirror, and SnapVault technologies.

### What SnapCenter Does

SnapCenter extends ONTAP's native data protection by adding:
- **Application awareness**: quiesces applications before taking snapshots (ensures consistency)
- **Centralized management**: single UI for managing backup policies across all applications and storage
- **Self-service restore**: application owners can restore their own data without storage admin involvement
- **Catalog and search**: searchable backup catalog for fast recovery
- **Policy automation**: automated backup schedules, retention, and verification
- **Integration with SnapMirror/SnapVault**: automatically mirrors/vaults backup snapshots to secondary storage or cloud

### SnapCenter Architecture

```
SnapCenter Server (Windows)
        │
        ├── SnapCenter UI (web browser)
        ├── SnapCenter REST API
        ├── Job scheduler and policy engine
        │
        ├── ONTAP Plugin ─────────────────────────→ ONTAP Clusters
        │   (communicates via ONTAP REST API)
        │
        ├── Application Plugins:
        │   ├── SnapCenter Plugin for VMware vSphere
        │   ├── SnapCenter Plugin for Oracle Database
        │   ├── SnapCenter Plugin for SQL Server
        │   ├── SnapCenter Plugin for SAP HANA
        │   ├── SnapCenter Plugin for Exchange
        │   ├── SnapCenter Plugin for Windows (filesystems)
        │   └── SnapCenter Plugin for Unix/Linux (filesystems)
        │
        └── SnapCenter Agents (installed on application hosts)
```

### Application-Consistent Backup Process

**Example: SQL Server backup with SnapCenter**
1. SnapCenter agent on SQL Server host signals SQL to **freeze I/O** (VSS quiesce)
2. ONTAP creates an **application-consistent Snapshot** of all SQL volumes simultaneously
3. SQL Server is **unfrozen** (I/O resumes) — total freeze time: typically <1 second
4. SnapCenter catalogues the backup with metadata (SQL instance, databases, timestamp)
5. SnapCenter triggers **SnapMirror/SnapVault update** to replicate snapshot to secondary

### SnapCenter Recovery Types

| Recovery Type | Description | Use Case |
|---|---|---|
| **Volume-level restore** | Restore entire volume from snapshot | Mass data loss, volume corruption |
| **Database restore** | Restore specific database files | Single database corruption |
| **Table/item restore** | Granular restore of specific tables or items | Accidental data deletion |
| **VM restore** | Restore entire VM or individual files (VMware) | VM corruption or deletion |
| **Clone** | Create a FlexClone from backup for testing | Test/dev, backup verification |

### SnapCenter vs Native SnapMirror/SnapVault

| Feature | Native ONTAP (CLI/API) | SnapCenter |
|---|---|---|
| Application consistency | Manual (scripts required) | Automatic (built-in plugins) |
| Centralized management | No (per-cluster) | Yes (single pane of glass) |
| Self-service restore | No | Yes (via SnapCenter UI) |
| Backup catalog | No | Yes (searchable) |
| Application plugins | No | Yes (Oracle, SQL, VMware, SAP HANA, etc.) |
| Granular recovery | Volume/file level | Application-level (tables, mailboxes, VMs) |
| Licensing | Included in ONTAP One | Separate SnapCenter license |

---

# MODULE 5 – Disaster Recovery and Backup to Cloud

## 5.1 Backup to Cloud: Use Cases

Cloud-based data protection has become an essential component of modern data protection strategies, driven by:

| Driver | Explanation |
|---|---|
| **Offsite protection** | Cloud is inherently geographically separated from on-premises — satisfies offsite copy requirement |
| **Cost efficiency** | Cloud object storage is significantly cheaper than secondary on-premises storage |
| **Scalability** | No capacity planning needed — cloud grows on demand |
| **Simplicity** | No secondary hardware to purchase, rack, and manage |
| **Air-gap capability** | Cloud copies can be isolated from on-premises (limiting ransomware reach) |
| **Global accessibility** | Recovery possible from any Internet-connected location |

### Cloud Protection Use Cases by Scenario

| Scenario | Cloud Protection Approach |
|---|---|
| Backup copy offsite | SnapMirror Cloud / BlueXP Backup |
| DR to cloud | Cloud Volumes ONTAP as DR target |
| Cloud-native application backup | BlueXP Backup for Amazon FSx / Azure NetApp Files |
| Long-term archive | Cloud object storage with Glacier/Archive tier |
| Ransomware air-gap | Immutable S3 object lock on backup copies |

---

## 5.2 Third-Party Software Integration

ONTAP's open REST API and standard protocols enable integration with leading third-party backup and data protection platforms.

### Common Third-Party Integrations

| Vendor / Product | Integration Method | Use Case |
|---|---|---|
| **Veeam Backup & Replication** | ONTAP REST API + SnapMirror | VM backup with ONTAP Snapshots; instant VM recovery |
| **Commvault** | ONTAP SnapMirror + API | Enterprise backup for ONTAP NAS/SAN + cloud |
| **Veritas NetBackup** | ONTAP API + NDMP | Enterprise backup, tape integration |
| **Cohesity DataProtect** | ONTAP Snapshot API | Modern backup for enterprise applications |
| **Rubrik** | ONTAP API | Cloud-first backup and recovery |
| **IBM Spectrum Protect** | ONTAP NDMP + API | Enterprise backup + tape |
| **Zerto** | Continuous replication + ONTAP | Continuous data protection and cloud DR |

### NDMP (Network Data Management Protocol)

**NDMP** is a standard protocol that enables backup applications to communicate directly with NAS storage systems for efficient backup and restore operations:

- Backup application issues NDMP commands to ONTAP
- ONTAP streams data directly to the backup target (tape, VTL, or disk)
- **Three-way NDMP**: ONTAP → backup media server → tape library
- **Two-way NDMP (local)**: ONTAP streams directly to attached tape device

### Veeam + ONTAP Integration (Deep Dive)

Veeam provides one of the deepest integrations with ONTAP:

- **ONTAP Snapshot for VM consistency**: Veeam can trigger ONTAP Snapshots as part of the VM backup workflow
- **SnapMirror integration**: Veeam can leverage SnapMirror replicas as backup sources (reduce production impact)
- **Instant VM Recovery from ONTAP**: boot VMs directly from ONTAP Snapshot/clone — no data copy needed
- **NAS Backup**: Veeam backs up ONTAP NAS shares with file-level granularity
- **SureBackup**: automated backup verification using FlexClone-based testing environments

---

## 5.3 Cloud Volumes ONTAP (CVO)

**Cloud Volumes ONTAP** is NetApp ONTAP software deployed as a **virtual storage appliance in public clouds** (AWS, Azure, GCP). It enables full ONTAP capabilities in the cloud, making it ideal as a cloud-based DR target.

### Cloud Volumes ONTAP as DR Target

```
On-Premises Production               Cloud DR Target
──────────────────────               ─────────────────────────────
  ONTAP Cluster (AFF/FAS)            Cloud Volumes ONTAP (CVO)
  Source SVM / Volume                 └── Destination SVM / Volume
        │                                         │
        └────── SnapMirror Async ────────────────►│
                (over Internet or                  │
                 Direct Connect)              AWS/Azure/GCP

On failover:
  - Break SnapMirror on CVO
  - Activate CVO as production
  - Mount via NFS/SMB/iSCSI from cloud-based hosts or VPN-connected on-prem hosts
```

### CVO Deployment Options

| Deployment | Configuration | Use Case |
|---|---|---|
| **Single node** | One CVO instance | Dev/test, non-critical workloads, cost-optimized |
| **HA pair** | Two CVO instances (active-active) | Production workloads with HA requirements |
| **Multi-AZ HA** | Two instances in different Availability Zones | Highest availability in cloud |

### CVO Key Features for Data Protection

- **Full SnapMirror compatibility**: CVO is a full ONTAP peer — SnapMirror works identically between on-prem ONTAP and CVO
- **FabricPool integration**: tier cold data from CVO to native cloud object storage (S3, Blob, GCS)
- **BlueXP management**: manage on-prem ONTAP and CVO from the same BlueXP interface
- **ONTAP One licensing**: apply existing ONTAP One licenses to CVO (BYOL model) or use pay-as-you-go
- **NVE encryption**: data at rest encrypted using ONTAP Volume Encryption

### Cost Optimization for CVO DR

For DR scenarios where CVO is only needed during a disaster:
- Run CVO at **minimum instance size** during normal operations (just receiving SnapMirror updates)
- **Scale up** the CVO instance at failover time to match production requirements
- Use cloud provider **reserved instance pricing** for predictable cost

---

## 5.4 BlueXP Backup and Recovery

**BlueXP Backup and Recovery** (formerly Cloud Backup) is NetApp's cloud-native backup-as-a-service solution, integrated directly into the BlueXP management platform.

### What BlueXP Backup and Recovery Does

BlueXP Backup and Recovery provides **policy-based backup of ONTAP volumes** directly to cloud object storage, without requiring a secondary ONTAP system.

```
SOURCE                                DESTINATION (Cloud Object Storage)
──────────────────────                ─────────────────────────────────────
On-premises ONTAP cluster    ──────►  AWS S3
Cloud Volumes ONTAP          ──────►  Azure Blob Storage
Amazon FSx for NetApp ONTAP  ──────►  Google Cloud Storage
Azure NetApp Files           ──────►  StorageGRID (on-prem object storage)
```

### BlueXP Backup Key Features

| Feature | Description |
|---|---|
| **Policy-based scheduling** | Define backup frequency (hourly, daily, weekly, monthly, yearly) and retention |
| **Incremental forever** | After the initial full backup, only changed blocks are backed up (space/bandwidth efficient) |
| **Data compression and deduplication** | Applied before data is sent to cloud — reduces cloud storage costs |
| **End-to-end encryption** | Data encrypted in transit (TLS) and at rest in object storage (AES-256) |
| **Object lock / immutability** | Integrates with AWS S3 Object Lock or Azure Immutable Blob for WORM protection |
| **DataLock and Ransomware Protection** | BlueXP-managed immutability layer for backup copies |
| **Catalogued search** | Search backup catalog to find specific files across all backup copies |
| **Granular file restore** | Restore individual files or directories from any backup copy |
| **Volume restore** | Restore entire volumes to the same or different system |
| **Cross-region restore** | Restore data to a different cloud region |
| **Cost tiering** | Automatically move older backup copies to cheaper cloud storage tiers (S3 Glacier, Azure Cool/Archive) |

### BlueXP Backup Policies

A BlueXP backup policy defines:

```
Policy: "production_backup_policy"
├── Hourly:   keep 24 copies  → stored in "Standard" tier
├── Daily:    keep 30 copies  → stored in "Standard" tier
├── Weekly:   keep 13 copies  → auto-moved to "Infrequent Access" after 30 days
├── Monthly:  keep 12 copies  → auto-moved to "Archive" after 90 days
└── Yearly:   keep 7 copies   → stored in "Archive" tier
              └── DataLock: enabled (immutable, ransomware-protected)
```

### BlueXP Backup Cost Estimation

BlueXP provides a **cost estimation tool** that projects monthly cloud storage costs based on:
- Volume size and data change rate
- Selected backup policy and retention
- Target cloud region and storage tier
- Compression and deduplication efficiency ratios

### Ransomware Protection with BlueXP Backup

BlueXP integrates with **ONTAP Autonomous Ransomware Protection (ARP)** and adds its own layer:

1. **ARP detects** suspicious activity on a source volume → creates immutable snapshot
2. **BlueXP backup** of that snapshot is sent to cloud object storage with **DataLock enabled**
3. The cloud backup copy is **immutable** — cannot be deleted by ransomware even if cloud credentials are compromised
4. Recovery: restore from the last clean backup copy in cloud, completely independent of on-premises infrastructure

---

## 5.5 Choosing the Right Data Protection Solution

### Decision Framework

```
START: What is your protection goal?
│
├── BACKUP/ARCHIVE
│   ├── Do you need application-consistent backup?
│   │   ├── Yes → SnapCenter (with app-specific plugins)
│   │   └── No  → Native SnapMirror vault policy or BlueXP Backup
│   ├── Where is the backup target?
│   │   ├── On-premises secondary → SnapVault (SnapMirror XDP vault)
│   │   └── Cloud                 → BlueXP Backup and Recovery
│   └── Long-term compliance/WORM?
│       ├── Yes → SnapLock Enterprise/Compliance + BlueXP DataLock
│       └── No  → Standard vault retention
│
├── DISASTER RECOVERY
│   ├── What is your RPO requirement?
│   │   ├── Hours   → SnapMirror Async (standard XDP)
│   │   ├── Minutes → SnapMirror Async (frequent schedule)
│   │   └── Zero    → SnapMirror Synchronous or Active Sync
│   ├── Is the DR target on-premises or cloud?
│   │   ├── On-premises → SnapMirror to secondary ONTAP
│   │   └── Cloud       → Cloud Volumes ONTAP + SnapMirror
│   └── Does the entire SVM need to be recoverable?
│       ├── Yes → SnapMirror for SVM DR
│       └── No  → Volume-level SnapMirror
│
└── BUSINESS CONTINUITY
    ├── What protocols must remain available?
    │   ├── SAN only (iSCSI/FC/NVMe) → SnapMirror Active Sync
    │   └── All protocols (NAS + SAN) → MetroCluster
    ├── What is acceptable RTO?
    │   ├── Near-zero (transparent) → SnapMirror Active Sync or MetroCluster
    │   └── Minutes                 → SnapMirror Synchronous
    └── What is the required distance between sites?
        ├── Any distance (<10ms RTT) → SnapMirror Active Sync
        └── Up to 700 km (strict HW) → MetroCluster IP
```

### Complete Solution Comparison Matrix

| Solution | RPO | RTO | Protocols | Scope | DR/BC/Backup | Cloud |
|---|---|---|---|---|---|---|
| **Snapshot** | Variable | Seconds | All | Volume/file | Backup | No |
| **SnapRestore** | Per snapshot | Seconds | All | File/LUN/Volume | Backup recovery | No |
| **SnapMirror Async** | Minutes | Minutes–Hours | All | Volume/SVM | DR | Via CVO |
| **SnapMirror Sync** | Zero | Minutes | NAS+SAN | Volume | DR | No |
| **SnapMirror Active Sync** | Zero | Near-zero | SAN | CG/Volume | BC | No |
| **SnapVault** | Hours | Hours | All | Volume | Backup | No |
| **MetroCluster** | Zero | ~30 sec | All | Cluster | BC | No |
| **SnapCenter** | App-consistent | App-level | All | App/VM | Backup | Via BlueXP |
| **Cloud Volumes ONTAP** | Minutes | Minutes–Hours | All | Volume/SVM | DR | Yes (AWS/Azure/GCP) |
| **BlueXP Backup** | Hours | Hours | All | Volume/File | Backup/Archive | Yes |
| **SnapMirror S3** | Minutes | Minutes | S3 | Bucket | DR/Backup | Yes |

---

# Summary – Conceptual Map

```
ONTAP DATA PROTECTION FUNDAMENTALS
│
├── 📋 DATA PROTECTION OVERVIEW
│   ├── Planning factors: RPO · RTO · Recovery scope · Consistency · Retention · Cost
│   ├── Snapshot: CoW, instantaneous, 0 initial space, up to 1023/vol
│   │   ├── Self-service restore via .snapshot (NAS) or Previous Versions (SMB)
│   │   └── Full volume revert (SnapRestore) in seconds
│   └── Relationships: cluster/SVM peering → source/destination → policy → schedule
│
├── 🔄 DISASTER RECOVERY
│   ├── SnapMirror Async
│   │   ├── RPO: minutes; RTO: minutes-hours
│   │   ├── Incremental block transfer (only changed blocks)
│   │   ├── Policies: MirrorAllSnapshots, MirrorAndVault, custom
│   │   ├── Fan-out (1 source → multiple destinations)
│   │   └── Cascade (source → intermediate → archive)
│   ├── SnapMirror for SVM DR
│   │   └── Replicates full SVM (volumes + LIFs + config + protocols)
│   ├── SnapMirror Synchronous
│   │   ├── RPO: zero; writes confirmed on both sites before host ACK
│   │   ├── Sync mode (fallback to async on disconnect) vs StrictSync (halt I/O)
│   │   └── Requires ≤10ms RTT
│   └── SnapMirror S3
│       └── Object-level replication to ONTAP S3 or cloud object storage
│
├── 🏢 BUSINESS CONTINUITY
│   ├── SnapMirror Active Sync
│   │   ├── RPO: zero; RTO: near-zero (transparent)
│   │   ├── Both sites serve I/O simultaneously (active-active SAN)
│   │   ├── SAN only (iSCSI, FC, NVMe/FC)
│   │   ├── ONTAP Mediator required (tiebreaker)
│   │   └── Consistency Groups for multi-volume apps
│   ├── MetroCluster
│   │   ├── Hardware-level RAID mirroring (SyncMirror)
│   │   ├── RPO: zero; RTO: ~30 seconds (auto switchover)
│   │   ├── All protocols (NAS + SAN)
│   │   ├── FC (300 km) or IP (700 km)
│   │   └── Switchover / Switchback commands
│   └── Comparison: SnapMirror Sync < Active Sync < MetroCluster (cost/complexity)
│
├── 💾 BACKUP AND ARCHIVE
│   ├── SnapVault (SnapMirror XDP vault)
│   │   ├── Independent long-term retention (days/weekly/monthly/yearly)
│   │   └── Source snapshot deletion does NOT affect vault copies
│   └── SnapCenter
│       ├── Application-aware (VSS quiesce: Oracle, SQL, VMware, SAP HANA)
│       ├── Centralized policy and scheduling
│       ├── Self-service restore portal
│       ├── Plugin ecosystem for all major apps
│       └── Orchestrates Snapshot + SnapMirror + SnapVault automatically
│
└── ☁️ CLOUD DR AND BACKUP
    ├── Third-party integration: Veeam · Commvault · Veritas · Cohesity · Rubrik
    ├── Cloud Volumes ONTAP (CVO)
    │   ├── Full ONTAP in AWS / Azure / GCP
    │   ├── SnapMirror target for cloud DR
    │   └── Scale up on failover
    └── BlueXP Backup and Recovery
        ├── Policy-based backup to cloud object storage (S3, Blob, GCS)
        ├── Incremental forever (block-level efficiency)
        ├── DataLock (immutability) + Object Lock integration
        ├── Cost tiering (Standard → IA → Archive)
        └── Granular file or full volume restore from cloud
```

---

*Documentation generated from NetApp course STRSW-WBT-DPFUND – ONTAP Data Protection Fundamentals*
*Extended detailed version – English*
