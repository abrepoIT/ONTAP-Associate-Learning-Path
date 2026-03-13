# Industry Essentials: Enterprise Storage
> **NetApp Course** | SALES-WBT-IESTR | Duration: 30 minutes | Format: Web-based training

---

# MODULE 1 – Data Center Infrastructure

## 1.1 What is a Data Center?

A **data center** is a physical facility dedicated to housing an organization's IT infrastructure. It is not just a "server room": it is a complex ecosystem that includes hardware, software, network connections, redundant power systems, advanced cooling, and physical and logical security systems.

### Primary Functions of a Data Center

- **Processing**: running business applications and workloads
- **Storage**: preserving and making data available
- **Networking**: connecting users, internal systems, and external networks (Internet)
- **Security**: physically and logically protecting IT resources
- **Business Continuity**: ensuring 24/7 availability through redundancy and failover

### Tier Classification (Uptime Institute)

The industry classifies data centers into **4 Tiers** based on guaranteed availability:

| Tier | Availability | Max Annual Downtime | Redundancy |
|---|---|---|---|
| **Tier I** | 99.671% | ~28 hours | No redundancy |
| **Tier II** | 99.741% | ~22 hours | Partial redundancy |
| **Tier III** | 99.982% | ~1.6 hours | N+1 (maintenance without downtime) |
| **Tier IV** | 99.995% | ~26 minutes | Fault-tolerant, fully redundant |

### Data Center Types

| Type | Description | Pros | Cons |
|---|---|---|---|
| **On-Premises** | Internally managed by the company | Full control, security | High costs, complex maintenance |
| **Colocation** | Physical space rented in a third-party facility | Savings on physical infrastructure | Less control over environment |
| **Cloud** | Virtualized and managed by a provider (AWS, Azure, GCP) | Scalability, pay-per-use | Provider dependency, latency |
| **Edge** | Distributed close to users or IoT devices | Low latency, local processing | Complex distributed management |
| **Hyperscale** | Enormous data centers (Google, Meta, Amazon) | Maximum efficiency, automation | Only for large organizations |

---

## 1.2 Enterprise IT Infrastructure Components

An enterprise IT infrastructure is composed of several interconnected layers. Each component plays a specific and critical role.

### Servers

Servers are high-performance computers designed to deliver services to other systems (clients or other servers).

**Types:**
- **Tower Server**: vertical format, used in small business environments
- **Rack Server**: mounted in standard rack cabinets (1U, 2U, 4U), very common in data centers
- **Blade Server**: ultra-compact, inserted into shared chassis (energy and space savings)
- **Modular/Converged**: integrate compute, storage, and networking in a single system

**Key server components:**
- CPU (one or more multi-core processors)
- RAM (volatile memory, ECC DRAM for reliability)
- Local storage (HDD/SSD/NVMe)
- Network cards (NIC – Network Interface Card)
- BMC/IPMI (out-of-band remote management)

### Storage

Devices dedicated to **data persistence**. Can be local (DAS) or network-shared (NAS/SAN). Covered in detail in the following modules.

### Networking

The network infrastructure connects all data center components to each other and to the outside world.

**Main components:**
- **Switch**: route traffic at L2 (MAC) and L3 (IP) levels
- **Router**: manage traffic between different networks
- **Firewall**: filter traffic and protect the network
- **Load Balancer**: distribute traffic across multiple servers
- **Top-of-Rack (ToR) Switch**: switches positioned at the top of each rack

**Common data center network topologies:**
- **Spine-Leaf**: two-tier architecture (spine = core, leaf = access), low latency and high scalability
- **Three-Tier**: core, distribution, access – classic but less scalable

### Virtualization

Virtualization allows running **multiple virtual machines (VMs)** on a single physical server through a **hypervisor**.

**Hypervisor types:**
- **Type 1 (Bare-metal)**: runs directly on hardware (VMware ESXi, Microsoft Hyper-V, KVM)
- **Type 2 (Hosted)**: runs on top of an OS (VirtualBox, VMware Workstation)

**Virtualization benefits:**
- Server consolidation (reduction of physical hardware)
- Workload isolation
- Live VM migration
- Simplified snapshots and backups

### Power and Cooling

**Power:**
- **UPS (Uninterruptible Power Supply)**: provides emergency power during blackouts
- **PDU (Power Distribution Unit)**: distributes power to racks
- **Diesel generators**: for extended outages
- Redundancy: N+1, 2N configurations

**Cooling:**
- **CRAC/CRAH (Computer Room Air Conditioning/Handler)**: dedicated air conditioning units
- **Hot aisle / Cold aisle**: rack organization to optimize airflow
- **Liquid cooling**: for high-density systems
- **PUE (Power Usage Effectiveness)**: energy efficiency metric (ideal: close to 1.0)

### Management Software

- **DCIM (Data Center Infrastructure Management)**: integrated monitoring of hardware, power, and cooling
- **Orchestration**: Kubernetes, VMware vCenter, OpenStack
- **Monitoring**: Nagios, Zabbix, Prometheus, Grafana
- **ITSM (IT Service Management)**: ServiceNow, JIRA Service Management

---

# MODULE 2 – How Storage Media Works

## 2.1 Types of Storage Media

### HDD – Hard Disk Drive

HDDs are **electromechanical** storage devices that use rotating magnetic platters to read and write data.

**Internal operation:**
1. One or more **platters** made of aluminum or glass coated with magnetic material spin at high speed (5,400 – 15,000 RPM)
2. **Read/write heads** float above the platters just nanometers away
3. An **actuator arm** positions the heads on the correct track
4. The **controller** manages I/O operations

**Disk data structure:**
- **Tracks**: concentric circles on each platter
- **Sectors**: segments of each track (typically 512 bytes or 4KB)
- **Cylinders**: set of tracks at the same radius across all platters
- **Clusters**: minimum filesystem allocation unit

**Performance parameters:**
- **Seek time**: time to position the head on the correct track (ms)
- **Rotational latency**: wait time for the sector to pass under the head (ms)
- **Transfer rate**: effective data transfer speed (MB/s)

**HDD Types:**
| Type | RPM | Typical Use |
|---|---|---|
| Desktop/NAS | 5,400 – 7,200 | Home, consumer NAS |
| Enterprise SATA | 7,200 | Secondary storage, backup |
| Enterprise SAS | 10,000 – 15,000 | Primary enterprise storage |
| SMR (Shingled Magnetic Recording) | Variable | Archive, high capacity |
| CMR/PMR | Variable | Standard, random writes |

---

### SSD – Solid State Drive

SSDs have no moving mechanical parts: data is stored in **NAND flash memory cells**.

**How it works:**
- NAND cells store data as **electrical charges** in floating-gate transistors
- The **SSD controller** manages: wear leveling, garbage collection, ECC (Error Correcting Code), caching
- The **DRAM cache** (present in high-end SSDs) accelerates read/write operations

**NAND cell types:**

| Type | Bits per cell | Density | Speed | Endurance (P/E cycles) | Cost |
|---|---|---|---|---|---|
| **SLC** (Single Level Cell) | 1 | Low | Maximum | ~100,000 | Very high |
| **MLC** (Multi Level Cell) | 2 | Medium | High | ~10,000 | High |
| **TLC** (Triple Level Cell) | 3 | High | Medium | ~3,000 | Medium |
| **QLC** (Quad Level Cell) | 4 | Very high | Low | ~1,000 | Low |
| **PLC** (Penta Level Cell) | 5 | Maximum | Low | <1,000 | Very low |

**Internal structure:**
- **Die**: physical NAND chip
- **Plane**: die subdivision for parallel operations
- **Block**: minimum erase unit (~256 pages)
- **Page**: minimum read/write unit (4KB – 16KB)

**Key SSD concepts:**
- **Wear Leveling**: distributes writes evenly across all cells to extend lifespan
- **Garbage Collection**: reclaims freed blocks to make them available for new writes
- **Over-provisioning**: reserved extra space (not visible) for internal management and performance
- **TRIM**: command that notifies the SSD which blocks no longer contain valid data
- **TBW (Terabytes Written)**: durability metric indicating how many TBs can be written over the SSD's lifetime

---

### NVMe – Non-Volatile Memory Express

NVMe is a **communication protocol** (not a media type) optimized for SSDs connected directly to the **PCIe** (PCI Express) bus.

**Why NVMe is faster than SATA/SAS:**
- SATA was designed for HDDs: supports 1 queue with 32 commands
- NVMe supports **65,535 queues** with **65,535 commands** each → maximum parallelism
- NVMe latency: ~20-100 µs vs ~100 µs (SATA SSD) vs ~5-10 ms (HDD)

**Common NVMe form factors:**
- **M.2**: small card, for laptops and desktops
- **U.2 / U.3**: 2.5" enterprise, for servers
- **EDSFF (E1.S, E3.S)**: new standards for high-density data centers
- **Add-in Card (AIC)**: full PCIe card

---

## 2.2 Full Comparison: HDD vs SSD vs NVMe

| Feature | HDD | SSD (SATA) | SSD (NVMe) |
|---|---|---|---|
| Sequential read speed | 100–200 MB/s | 500–600 MB/s | 3,000–7,000 MB/s |
| Sequential write speed | 100–200 MB/s | 400–550 MB/s | 2,000–6,500 MB/s |
| IOPS (4K random read) | 100–200 | 80,000–100,000 | 500,000–1,000,000+ |
| Latency | 5–10 ms | ~100 µs | 20–100 µs |
| Max capacity (consumer) | 20+ TB | 4–8 TB | 4–8 TB |
| Max capacity (enterprise) | 30+ TB | 15+ TB | 30+ TB (U.2) |
| Reliability | Subject to mechanical failure | High | High |
| Typical power (W) | 6–10 W | 2–5 W | 5–10 W |
| Noise | Yes | No | No |
| Cost per TB | Low (~$15–30) | Medium (~$60–100) | High (~$100–200+) |

---

# MODULE 3 – Components of a Storage System

## 3.1 What is a Storage System (Storage Array)?

A **storage array** is a hardware system dedicated exclusively to managing and delivering storage capacity to servers. Unlike local storage (DAS), it offers:

- **Sharing**: multiple servers can access the same data
- **Hardware redundancy**: multiple controllers, redundant power supplies, hot-spare drives
- **Advanced management**: snapshots, replication, tiering, deduplication, compression
- **High availability**: automatic failover in case of component failure

---

## 3.2 Controller Architecture

The **controller** is the central component of every storage array. It manages all I/O traffic between host servers and physical drives.

### Internal Controller Components

| Component | Function |
|---|---|
| **CPU** | Processes I/O operations, manages RAID logic and software features |
| **RAM/DRAM** | High-speed read and write cache |
| **NVRAM / NVDIMM** | Non-volatile write cache (data persistent even during power loss) |
| **Host Ports** | Connection ports toward servers (FC, iSCSI, NVMe-oF, NFS, SMB) |
| **Drive Ports** | Connection ports toward drive shelves (SAS, NVMe) |
| **Management Port** | Dedicated port for out-of-band array management |
| **Firmware** | Embedded software that manages the entire system operation |

### Dual Controller (HA – High Availability)

The vast majority of enterprise storage systems use **two identical controllers** in Active-Active or Active-Passive configuration:

- **Active-Active**: both controllers process I/O simultaneously → maximum performance and transparent failover
- **Active-Passive**: one controller is primary, the other on standby → slower failover but simpler

In case of a controller failure, the other **automatically takes over** (takeover) without service interruption.

---

## 3.3 Caching

Cache is fundamental to the performance of a modern storage system.

### Read Cache

Stores in high-speed memory (DRAM or NVMe) recently read or most frequently accessed data.

- **Hit ratio**: percentage of requests served from cache (higher = better)
- **Algorithms**: LRU (Least Recently Used), LFU (Least Frequently Used), predictive prefetching

### Write Cache

Accepts writes in fast memory before committing data to disk:

| Mode | How it works | Advantages | Risks |
|---|---|---|---|
| **Write-Back** | Data written to cache, immediate ACK to host, flushed to disk in background | High performance | Risk of data loss without cache protection |
| **Write-Through** | Data written simultaneously to cache and disk, ACK after disk confirmation | Safe | Slower |

**Write Cache Protection:**
- **NVRAM**: RAM with battery or supercapacitors → data preserved even during power loss
- **Cache mirroring**: cache content replicated to the second controller

### Multi-Tier Caching

Advanced systems implement multiple cache levels:
```
L1: DRAM (ultra-fast, expensive, small)
L2: NVMe SSD (very fast, medium cost, larger)
L3: SAS/SATA SSD (fast, affordable, large)
L4: HDD (slow, very cheap, enormous)
```

**Automatic tiering software** moves data between levels based on access frequency ("hot" data = upper tiers, "cold" data = lower tiers).

---

## 3.4 Storage Cluster and Internal Networking

### Storage Cluster

Multiple storage arrays (nodes) can be connected together to form a **cluster** that presents itself as a single logical system.

**Clustering benefits:**
- **Scalability**: add nodes to increase capacity and IOPS
- **Load balancing**: automatic distribution of workloads
- **High availability**: no single point of failure
- **Global namespace**: all data accessible from any cluster node

**Cluster internal network:**
- Dedicated high-speed switches (10/25/40/100 GbE or InfiniBand)
- Low latency is critical for data consistency

### Example: NetApp ONTAP Cluster

NetApp ONTAP can be configured as a cluster of up to 24 nodes. Data is distributed across all nodes through **aggregates** and **volumes**, with transparent access regardless of which node handles the request.

---

## 3.5 Scale-Up vs Scale-Out

### Scale-Up (Vertical Scaling)

Expands the system by **adding resources to the existing system**: more drives, more shelves, more cache, more powerful processors.

**Practical example:**
- An array starts with 24 drives → expansion shelves are added → reaches 100+ drives
- HDDs are replaced with SSDs to increase performance

**When to use it:**
- Workload consolidated on a single system
- Management simplicity is a priority
- Limited budget for complexity

**Limitations:**
- Physical limit of the system (maximum supported drives, ports, memory)
- Upgrades often require downtime
- Controller bottleneck if not upgraded

### Scale-Out (Horizontal Scaling)

Expands the system by **adding new nodes** to the existing cluster. Capacity, IOPS, and bandwidth grow nearly linearly.

**When to use it:**
- Rapid and unpredictable data growth
- Distributed workloads (object storage, big data, analytics)
- Need for "infinite" scalability

**Limitations:**
- Greater management complexity
- Intra-cluster latency (inter-node communication)
- Higher initial cost

| Aspect | Scale-Up | Scale-Out |
|---|---|---|
| Complexity | Low | Medium-High |
| Maximum scalability | Limited | Nearly unlimited |
| Initial cost | Low | Medium-High |
| Linear performance | No | Yes |
| Management | Single system | Distributed cluster |
| Ideal for | Single DB, legacy apps | Cloud, big data, object storage |

---

## 3.6 Storage System Options

### All-Flash Array (AFA)

Uses **only SSDs or NVMe**. Designed for maximum performance.

- Sub-millisecond latency
- Millions of IOPS
- Data efficiency (dedup, compression) to reduce effective cost per GB
- Ideal for: OLTP databases, VDI, mission-critical applications

**Examples:** NetApp AFF, Pure Storage FlashArray, Dell PowerStore

### Hybrid Array

Mix of **SSDs (hot tier) and HDDs (cold tier)**. Automatic tiering software moves data between levels.

- Good cost/performance balance
- Transparent tiering management
- Ideal for: mixed workloads, environments that don't always require maximum performance

**Examples:** NetApp FAS, Dell PowerVault

### Object Storage

Stores data as **objects** (not files or blocks), each with a unique ID and extended metadata.

- Scales to petabytes and beyond
- Access via REST API (S3, Swift)
- Ideal for: backup, archive, media, big data, cloud-native

**Examples:** NetApp StorageGRID, MinIO, Ceph

### Software-Defined Storage (SDS)

Storage software runs on **commodity x86 hardware**.

- Separation between hardware and software
- Flexibility and portability
- Examples: VMware vSAN, NetApp ONTAP Select, Ceph

---

# MODULE 4 – Storage Connectivity and Interfaces

## 4.1 Server Storage Connectivity – Overview

Connectivity between servers and storage is one of the most critical factors for IT infrastructure performance. Technology choice depends on: required latency, bandwidth, distance, budget, and application protocol.

```
SERVER
  │
  ├── DAS (Direct Attached Storage) ──── HDD/SSD directly on the server
  │
  ├── SAN (Storage Area Network) ─────── Dedicated network, block-level access
  │     ├── Fibre Channel (FC)
  │     ├── iSCSI
  │     ├── FCoE
  │     └── NVMe-oF (NVMe over Fabrics)
  │
  └── NAS (Network Attached Storage) ─── IP network, file-level access
        ├── NFS (Linux/Unix)
        └── SMB/CIFS (Windows)
```

---

## 4.2 DAS – Direct Attached Storage

Storage is **directly connected to the server** without going through a network.

**Common DAS interfaces:**
- **SATA III**: up to 600 MB/s, affordable, for consumer HDDs and SSDs
- **SAS (Serial Attached SCSI)**: up to 12 Gbps (SAS-3) or 24 Gbps (SAS-4), enterprise, reliable
- **NVMe (PCIe)**: up to 32 GB/s (PCIe 4.0 x4), minimum latency

**DAS use cases:**
- Standalone servers with local storage
- Hyper-converged infrastructure (HCI) like VMware vSAN, Nutanix
- Applications with extreme latency requirements (high-frequency trading)
- OS boot drive

**DAS limitations:**
- Not shareable between multiple servers (unless using software clustering like HCI)
- If the server fails, storage may become inaccessible
- Fragmented management (each server has its own storage)

---

## 4.3 SAN – Storage Area Network

A SAN is a **dedicated high-speed network** that connects servers and storage at the **block level**. Servers see LUNs (Logical Unit Numbers) as local physical disks.

### Fibre Channel (FC)

The enterprise SAN protocol par excellence.

**Characteristics:**
- Speed: 8, 16, 32, 64 Gbps
- Low latency and high reliability
- Completely separate network from IP network (requires dedicated FC switches = **directors** or **fabric switches**)
- Protocol: FCP (Fibre Channel Protocol)

**Components:**
- **HBA (Host Bus Adapter)**: server card for FC connection
- **FC Switch / Director**: Fibre Channel network switch
- **Target Port**: storage array ports

**FC Topologies:**
- **Point-to-Point**: direct connection between server and storage (rare)
- **Arbitrated Loop (FC-AL)**: obsolete
- **Switched Fabric**: each device connects to the switch, scalable (modern standard)

**WWN (World Wide Name):** unique address of each FC port (similar to MAC address in Ethernet)

---

### iSCSI – Internet Small Computer Systems Interface

Transports the **SCSI protocol over TCP/IP**, allowing SAN creation using standard Ethernet networking.

**Characteristics:**
- Speed: 1, 10, 25, 40, 100 GbE
- More affordable than FC (uses standard Ethernet switches)
- Simpler configuration
- Slightly higher latency than FC

**Components:**
- **iSCSI Initiator**: software or hardware (iSCSI HBA) in the server
- **iSCSI Target**: storage array port
- **iSNS (Internet Storage Name Service)**: iSCSI resource discovery service

**Best practices:**
- Dedicated VLAN for iSCSI traffic
- Multipath I/O (MPIO) for redundancy and load balancing
- Jumbo Frames (MTU 9000) to reduce overhead

---

### FCoE – Fibre Channel over Ethernet

Encapsulates **Fibre Channel traffic over 10GbE+ Ethernet**, allowing FC and IP networks to be unified on a single Ethernet infrastructure.

- Requires **DCB (Data Center Bridging)** to guarantee "lossless" Ethernet
- Converged Network Adapter (**CNA**) replaces both NIC and HBA

---

### NVMe-oF – NVMe over Fabrics

Extends the **NVMe protocol** over storage networks, bringing the performance of local NVMe storage to shared architectures.

**Supported transports:**
- **NVMe/FC**: NVMe over Fibre Channel
- **NVMe/RoCE**: NVMe over Ethernet with RDMA (latency <10 µs)
- **NVMe/TCP**: NVMe over standard TCP/IP (easier to implement)

**Why NVMe-oF is revolutionary:**
- Latency comparable to local NVMe storage
- Suitable for AI/ML applications, in-memory databases, real-time analytics

---

## 4.4 NAS – Network Attached Storage

A NAS is a storage system that exposes data as **shared file systems** through standard IP networking.

### NFS – Network File System

Protocol developed by Sun Microsystems, the de facto standard for **Linux/Unix** environments.

| Version | Key Features |
|---|---|
| **NFSv3** | Stateless, widely supported, simple |
| **NFSv4** | Stateful, improved security (Kerberos), ACL support |
| **NFSv4.1** | pNFS (parallel NFS), parallel access to multiple servers |
| **NFSv4.2** | Server-side copy, sparse files, labeled NFS |

### SMB/CIFS – Server Message Block

Microsoft protocol for file sharing in **Windows** environments.

| Version | Windows | Features |
|---|---|---|
| **SMB 1.0** | XP/2003 | Obsolete, vulnerable (WannaCry) |
| **SMB 2.0** | Vista/2008 | Reduced chattiness, better performance |
| **SMB 2.1** | 7/2008 R2 | Large MTU, BranchCache |
| **SMB 3.0** | 8/2012 | Multichannel, encryption, SMB Direct (RDMA) |
| **SMB 3.1.1** | 10/2016+ | Pre-auth integrity, AES-128 encryption |

### NAS vs SAN – Comparison

| Feature | NAS | SAN |
|---|---|---|
| Access level | File | Block |
| Protocols | NFS, SMB/CIFS | FC, iSCSI, NVMe-oF |
| Sharing | Native (multiple users/servers) | Requires cluster filesystem |
| Latency | Higher | Lower |
| Complexity | Low | High |
| Cost | Lower | Higher |
| Typical use | File sharing, home dirs, web | DB, VMs, mission-critical apps |

---

## 4.5 Storage Interfaces – Full Summary

| Interface | Type | Max Speed | Protocol | Typical Use |
|---|---|---|---|---|
| **SATA III** | DAS | 600 MB/s | ATA | Consumer HDD/SSD |
| **SAS-3** | DAS | 12 Gbps | SCSI | Enterprise HDD/SSD |
| **SAS-4** | DAS | 24 Gbps | SCSI | Future enterprise HDD |
| **NVMe (PCIe 4.0)** | DAS | ~7 GB/s | NVMe | Ultra-performance SSD |
| **Fibre Channel 32G** | SAN | 32 Gbps | FCP | Enterprise storage |
| **iSCSI 25GbE** | SAN | 25 Gbps | SCSI/TCP | SAN over Ethernet |
| **NVMe/TCP** | SAN | 100 GbE+ | NVMe | AI/HPC storage |
| **NFS v4.1** | NAS | Network-dependent | NFS | Linux file sharing |
| **SMB 3.1.1** | NAS | Network-dependent | SMB | Windows file sharing |

---

# MODULE 5 – Cloud Storage

## 5.1 Cloud Computing – Fundamentals

**Cloud computing** is a model for delivering IT services over the Internet, based on five essential characteristics (NIST definition):

1. **On-demand self-service**: automatic resource provisioning without provider human intervention
2. **Broad network access**: access from any device with an Internet connection
3. **Resource pooling**: resources shared among multiple users (multi-tenancy)
4. **Rapid elasticity**: instant scale up and down
5. **Measured service**: payment based on actual consumption (pay-per-use)

### Major Cloud Providers

| Provider | Market Share | Strengths |
|---|---|---|
| **AWS (Amazon)** | ~31% | Widest service catalog, greatest maturity |
| **Azure (Microsoft)** | ~25% | Microsoft ecosystem integration, hybrid |
| **GCP (Google)** | ~11% | AI/ML, big data, Kubernetes (GKE) |
| **Alibaba Cloud** | ~4% | Asian market |
| **Oracle Cloud** | ~2% | Database, enterprise ERP |

---

## 5.2 Cloud Service Models

### IaaS – Infrastructure as a Service

The provider offers **virtualized infrastructure resources**: VMs, storage, networking. The customer manages the OS, middleware, and applications.

**Responsibilities:**
- Provider: physical hardware, hypervisor, physical network, physical security
- Customer: OS, patching, middleware, applications, data, virtual network configuration

**Examples:** AWS EC2 + EBS, Azure Virtual Machines + Managed Disks, Google Compute Engine

**Use cases:** lift-and-shift of on-premises applications, dev/test environments, web hosting

---

### PaaS – Platform as a Service

The provider offers a **complete platform** to develop, run, and manage applications, without the customer managing the underlying infrastructure.

**Responsibilities:**
- Provider: everything up to runtime and middleware
- Customer: applications and data

**Examples:** Google App Engine, Azure App Service, Heroku, AWS Elastic Beanstalk

**Use cases:** rapid web/mobile application development, DevOps, CI/CD

---

### SaaS – Software as a Service

**Complete applications** accessible via browser, without managing any infrastructure.

**Responsibilities:**
- Provider: everything
- Customer: only their own data and user configurations

**Examples:** Microsoft 365, Google Workspace, Salesforce, Zoom, Dropbox

**Use cases:** business productivity, CRM, ERP, collaboration

---

### Additional Models

| Model | Description | Examples |
|---|---|---|
| **FaaS** (Function as a Service) | Execution of individual serverless functions | AWS Lambda, Azure Functions |
| **DBaaS** (Database as a Service) | Managed databases without administration | AWS RDS, Azure SQL, Google Cloud SQL |
| **STaaS** (Storage as a Service) | Pure cloud storage | AWS S3, Azure Blob, Google Cloud Storage |
| **DRaaS** (Disaster Recovery as a Service) | Cloud-based DR | Zerto, Veeam Cloud Connect |

---

## 5.3 Cloud Deployment Types

### Public Cloud

Infrastructure **shared among multiple organizations** (multi-tenant), managed by a provider.

- No upfront investment (CapEx → OpEx)
- Immediate scalability
- Physical security responsibility on the provider
- Risks: compliance, data residency, vendor lock-in

### Private Cloud

Infrastructure **dedicated to a single organization**, managed internally or by a dedicated provider.

- Maximum control and customization
- Better isolation and security
- Suitable for highly sensitive data (banking, healthcare, government)
- Higher costs

**Technology examples:** VMware Cloud Foundation, OpenStack, NetApp Private Storage

### Hybrid Cloud

Combination of **public and private cloud**, integrated to share data and workloads.

- Sensitive workloads on-premises, elastic workloads on public cloud
- **Cloud bursting**: during peak loads, overflow to public cloud
- Requires dedicated connectivity (VPN, ExpressRoute, Direct Connect)

**Challenges:** latency, security of data in transit, policy consistency

### Multi-Cloud

Use of **multiple public cloud providers simultaneously**.

- Avoids vendor lock-in
- Cost optimization (best service for each workload)
- Greater resilience (one provider's downtime doesn't impact everything)
- Challenge: centralized management, data governance

---

## 5.4 Cloud Storage Services and Types

### Object Storage

**How it works:** data is stored as **objects** with a unique ID (hash), raw data, and extended metadata. There is no folder hierarchy (flat namespace).

- Access via **REST API** (standard: Amazon S3)
- Scales to exabytes
- Built-in geographic redundancy
- Ideal for: backup, archive, media, unstructured data, static websites

**Examples:** AWS S3, Azure Blob Storage, Google Cloud Storage, NetApp StorageGRID

### Block Storage

**How it works:** storage is presented as **block volumes** (like virtual disks). The filesystem is managed by the server's operating system.

- Low latency
- Suitable for databases, VMs, applications requiring direct disk access
- Not natively shareable between multiple VMs (only with cluster filesystem)

**Examples:** AWS EBS (Elastic Block Store), Azure Managed Disks, Google Persistent Disk

### File Storage

**How it works:** storage accessible as a **shared file system** via NFS or SMB.

- Shareable between multiple VMs/instances simultaneously
- Ideal for: legacy applications, CMS, shared development environments, home directories

**Examples:** Amazon EFS (NFS), Amazon FSx for Windows (SMB), Azure Files, Google Filestore

### Archive Storage

**How it works:** very low-cost storage for **rarely or never accessed** data, with retrieval times ranging from minutes to hours.

- Extremely low cost (a few $/TB/month)
- Ideal for: long-term compliance, historical backups, legal data

**Examples:** AWS S3 Glacier, Azure Archive, Google Coldline

### Cloud Storage Classes Comparison

| Type | Latency | Cost | Sharing | Typical Use |
|---|---|---|---|---|
| Block | ms | High | No (single host) | DB, VM, OS |
| File | Low ms | Medium | Yes (multi-host) | File sharing, dev |
| Object | ms to s | Low | Yes (via API) | Backup, media, archive |
| Archive | Minutes-hours | Very low | No | Compliance, historical archive |

---

## 5.5 Cloud Storage Use Cases

| Use Case | Storage Type | Example |
|---|---|---|
| Backup and DR | Object/Archive | Nightly backup to S3 Glacier |
| Static website hosting | Object | S3 + CloudFront CDN |
| Production database | Block | RDS on EBS io2 |
| Team file sharing | File | Azure Files + Azure AD |
| Video streaming / media | Object | S3 + CDN |
| Big Data & Analytics | Object | Data Lake on S3/ADLS |
| Legal/compliance archive | Archive | S3 Glacier Deep Archive |
| VM backup | Block + Object | Snapshot → Object storage |

---

# MODULE 6 – Critical Storage Aspects

## 6.1 Data Availability

**Availability** is measured as the percentage of time a service is operational (uptime).

| Availability | Annual Downtime | Monthly Downtime |
|---|---|---|
| 99% | ~87 hours | ~7.3 hours |
| 99.9% ("three nines") | ~8.7 hours | ~43 minutes |
| 99.99% ("four nines") | ~52 minutes | ~4.4 minutes |
| 99.999% ("five nines") | ~5 minutes | ~26 seconds |

### RAID – Redundant Array of Independent Disks

RAID distributes data across multiple physical drives to ensure **performance and/or redundancy**.

#### RAID 0 – Striping
- Data distributed across multiple drives with no redundancy
- Maximum performance, total capacity = sum of all drives
- **No fault tolerance**: if one drive fails, all data is lost
- Use: video editing, temporary high-performance environments

#### RAID 1 – Mirroring
- Every piece of data is written to two drives simultaneously
- Tolerates failure of 1 drive (or more with >2 drives)
- Capacity = 50% of total
- Use: OS boot drive, critical systems

#### RAID 5 – Striping with distributed parity
- Data and parity information distributed across N drives (min. 3)
- Tolerates failure of **1 drive**
- Capacity = (N-1) × drive size
- Requires **rebuild** after failure (performance drops and risk increases during rebuild)
- Use: NAS, secondary storage, file servers

#### RAID 6 – Striping with double parity
- Like RAID 5 but with **two parity blocks** per stripe
- Tolerates failure of **2 drives simultaneously**
- Capacity = (N-2) × drive size
- Use: storage with high-capacity drives (long rebuild = higher risk)

#### RAID 10 (1+0) – Mirroring + Striping
- Creates mirror sets first (RAID 1), then stripes across them (RAID 0)
- Tolerance: at least 1 drive per mirror pair
- Capacity = 50% of total
- Excellent performance, good redundancy
- Use: databases, mission-critical virtualized environments

#### RAID-DP / Triple Parity (NetApp-specific)
- **NetApp RAID-DP**: double parity for large SATA drives
- **NetApp RAID-TEC**: triple parity for drives >8TB

#### Hot Spare
A **hot spare** is an unused drive that is automatically integrated into the RAID upon failure, immediately starting the rebuild without human intervention.

---

### System-Level High Availability (HA)

- **Redundant controllers**: automatic failover in <30 seconds
- **Redundant power supplies**: N+1 or 2N configuration
- **Redundant network ports**: multipathing on the host side
- **Hot-swappable drives**: hot replacement without shutting down the system
- **Redundant expansion shelves**: multiple paths to drives

---

## 6.2 Data Protection

### Snapshots

A **snapshot** is a point-in-time copy of the state of a volume or filesystem, created almost instantaneously.

**How it works (Copy-on-Write):**
1. The snapshot is created: initially it **consumes no additional space**
2. When the original data is modified, the old value is **copied to the snapshot space** before being overwritten
3. The snapshot consumes space only for changes made after its creation

**Use cases:**
- Fast recovery from human errors (accidentally deleted files)
- Application-consistent backup (snapshot + backup)
- Testing and development (production clone in seconds)
- OS/application updates (immediate rollback if something goes wrong)

**Limitations:**
- Not a backup substitute (resides on the same system)
- Consumes space over time if data changes frequently

---

### Backup

A **backup** is a copy of data on a **separate medium** (different system, different location).

#### Backup Types

| Type | What it saves | Space | Backup time | Restore time |
|---|---|---|---|---|
| **Full** | All data | High | Long | Short |
| **Incremental** | Only changes since the last backup (full or incr.) | Low | Short | Long (chain) |
| **Differential** | Only changes since the last full | Medium | Medium | Medium |
| **Synthetic Full** | Full built from full + incrementals (without re-reading original data) | Medium | Short | Short |

#### The 3-2-1 Rule

A fundamental best practice for data protection:
- **3 copies** of the data
- On **2 different types** of media
- With **1 offsite copy** (remote or cloud)

**Modern version: 3-2-1-1-0**
- 3 copies, 2 media types, 1 offsite, **1 offline/air-gapped copy**, **0 errors verified** (regular restore testing)

---

### Replication

**Replication** creates a copy of data on a **remote system** (disaster recovery site or cloud).

#### Synchronous Replication
- Every write is confirmed **only after** data has been written to both the primary and secondary site
- **RPO = 0** (no data loss)
- Requires low network latency (<5ms, ideally <1ms)
- Performance impact (application must wait)

#### Asynchronous Replication
- Writes are confirmed on the primary site, then replicated in the background
- **RPO > 0** (possible data loss equal to the replication lag)
- Works on WAN networks with high latency
- Lower performance impact
- Typically used for geographically distant DR

---

### Business Continuity and Disaster Recovery (BC/DR)

| Metric | Description | Example |
|---|---|---|
| **RPO** (Recovery Point Objective) | Maximum tolerable data loss | RPO = 1 hour → can lose at most 1 hour of data |
| **RTO** (Recovery Time Objective) | Maximum time to restore the service | RTO = 4 hours → must be operational within 4 hours |
| **MTTR** (Mean Time To Repair) | Average time to repair a failed component | Indicates support team speed |
| **MTBF** (Mean Time Between Failures) | Average time between one failure and the next | Indicates system reliability |

#### DR Tiers

| Tier | Technology | RTO | RPO | Cost |
|---|---|---|---|---|
| **Offsite tape backup** | Tape backup | Hours-days | Hours-days | Low |
| **Remote disk backup** | Vaulting | Hours | Hours | Medium |
| **Async replication** | Async replication | Minutes-hours | Minutes | Medium-high |
| **Sync replication** | Sync replication | Minutes | 0 | High |
| **Active-Active** | Geo-distributed cluster | Seconds | 0 | Very high |

---

## 6.3 Data Security

### Encryption

#### Encryption At-Rest
Data is **encrypted when written to disk** and decrypted when read.

- **Self-Encrypting Drive (SED)**: the drive itself performs hardware encryption
- **Software encryption**: performed by the controller or storage software (AES-256)
- Protects against physical drive theft

#### Encryption In-Transit
Data is encrypted **during network transmission**.

- **TLS 1.2/1.3**: for NFS, SMB, web management, REST APIs
- **IPsec**: for iSCSI and IP communications
- **FC-SP-2 (Fibre Channel Security Protocol)**: for FC networks

### Access Control

#### Authentication
Verifies **who you are**:
- Username/Password
- **MFA (Multi-Factor Authentication)**: password + token + biometrics
- **Kerberos**: secure authentication for NFSv4 and SMB
- Digital certificates

#### Authorization
Defines **what you can do**:
- **RBAC (Role-Based Access Control)**: permissions assigned by role
- **ACL (Access Control List)**: granular permissions per user/group on files and directories
- **FC Zoning**: limits which HBAs can access which storage ports
- **LUN Masking**: limits which servers can see which LUNs

### Ransomware Protection

Ransomware is a critical threat to storage systems. Defense strategies include:

- **Immutable Snapshots (WORM)**: snapshots that cannot be deleted before an expiry date
- **NetApp SnapLock**: WORM technology for regulated data and ransomware protection
- **Air-gapping**: isolated copy with no network connection
- **Anomaly detection**: identifying abnormal behavior (e.g., massive file encryption)
- **Zero Trust Architecture**: no user or system is trusted by default

### Audit Logging

- Tracking all data accesses, modifications, and deletions
- Essential for forensics in case of an incident
- Required by regulations (GDPR, HIPAA, SOX)

---

## 6.4 Data Governance, Risk & Compliance

### Data Governance

**Data governance** defines who is responsible for data, how it is managed, and what policies apply throughout its lifecycle.

**Key elements:**

| Element | Description |
|---|---|
| **Data Ownership** | Who is responsible for a specific dataset |
| **Data Stewardship** | Who operationally manages quality and access |
| **Data Catalog** | Centralized inventory of all corporate data |
| **Data Classification** | Categorization by sensitivity level (Public, Internal, Confidential, Secret) |
| **Data Lineage** | Traceability of data origin and transformations |
| **Retention Policy** | How long to retain each type of data |
| **Data Quality** | Monitoring and correction of data quality |

### Risk Management

**Risk management** identifies, assesses, and mitigates data-related risks:

1. **Risk Identification**: what risks exist? (data loss, breach, non-compliance)
2. **Risk Assessment**: what is the likelihood and impact?
3. **Risk Mitigation**: what controls to implement?
4. **Risk Monitoring**: are the controls working?

**Main storage risks:**
- Data loss (hardware failure, human error, disaster)
- Data breach (unauthorized access)
- Regulatory non-compliance (fines, reputational damage)
- Vendor lock-in
- Service disruption (ransomware, DDoS attacks)

### Compliance

**Compliance** ensures that data management conforms to applicable regulations.

#### Major Regulations

| Regulation | Area | Key Storage Requirements |
|---|---|---|
| **GDPR** (EU) | Personal data privacy | Data residency, right to erasure, breach notification |
| **HIPAA** (USA) | Healthcare data | Encryption, access control, audit log, backup |
| **SOX** (USA) | Financial data | Integrity and immutability of financial records |
| **PCI-DSS** | Credit card data | Encryption, network segmentation, access control |
| **ISO 27001** | Information security | Global framework for ISMS |
| **NIS2** (EU) | Network and info security | Security measures for critical infrastructures |

#### WORM – Write Once, Read Many

Technology that guarantees **data immutability** for the period defined by the retention policy:
- Data can be written once and cannot be modified or deleted before expiration
- Fundamental for regulatory compliance
- **NetApp SnapLock Compliance**: the copy cannot be deleted even by the administrator
- **NetApp SnapLock Enterprise**: allows the administrator to delete data in exceptional cases

---

## Final Summary – Conceptual Map

```
ENTERPRISE STORAGE
│
├── 🏢 DATA CENTER
│   ├── Tier I → IV (increasing availability)
│   ├── On-prem / Colo / Cloud / Edge / Hyperscale
│   └── Server · Storage · Network · Virtualization · Power · Cooling
│
├── 💾 STORAGE MEDIA
│   ├── HDD: magnetic platters, affordable, slow (100-200 MB/s)
│   ├── SSD SATA: NAND flash, fast (500 MB/s), SLC>MLC>TLC>QLC
│   └── NVMe: PCIe, ultra-fast (7 GB/s), low latency (µs)
│
├── 🖥️ STORAGE SYSTEMS
│   ├── Controller (dual HA, DRAM+NVRAM cache, RAID)
│   ├── Write-Back vs Write-Through cache
│   ├── Automatic tiering (hot→warm→cold)
│   ├── Scale-Up (add resources) vs Scale-Out (add nodes)
│   └── AFA · Hybrid · Object · SDS
│
├── 🔌 CONNECTIVITY
│   ├── DAS: direct (SATA, SAS, NVMe)
│   ├── SAN: block network (FC 32G, iSCSI, FCoE, NVMe-oF)
│   └── NAS: file network (NFS v4, SMB 3.1)
│
├── ☁️ CLOUD STORAGE
│   ├── Service models: IaaS · PaaS · SaaS · FaaS · DBaaS
│   ├── Types: Public · Private · Hybrid · Multi-Cloud
│   └── Storage: Object(S3) · Block(EBS) · File(EFS) · Archive(Glacier)
│
└── 🔒 CRITICAL ASPECTS
    ├── Availability: RAID0/1/5/6/10/DP · HA · Hot Spare
    │   └── 99% → 99.999% uptime
    ├── Protection: Snapshot(CoW) · Backup(3-2-1-1-0) · Replication(sync/async)
    │   └── RPO (data loss) · RTO (recovery time)
    ├── Security: Encryption(AES-256) · RBAC · ACL · Anti-ransomware
    │   └── At-rest · In-transit · Zoning · LUN Masking · Audit Log
    └── Governance: GDPR · HIPAA · SOX · ISO 27001 · WORM · SnapLock
        └── Data classification · Retention · Lineage · Risk Management
```

---

*Documentation generated from NetApp course SALES-WBT-IESTR – Industry Essentials: Enterprise Storage*
*Extended detailed version – English*
