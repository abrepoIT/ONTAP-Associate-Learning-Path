# Industry Essentials: Cloud Computing
> **NetApp Course** | SALES-WBT-CLCMFUND | Duration: 30 minutes | Format: Web-based training

---

## Course Overview

Cloud computing is one of the major forces shaping how IT teams design, build, and operate enterprise infrastructure. This course covers the fundamentals of cloud computing: core terminology, service delivery models, deployment types, key characteristics, and the critical evaluation criteria that organizations must consider before adopting cloud services — including security, privacy, compliance, performance, and vendor lock-in.

---

## Course Objectives

By the end of this course, you will be able to:

- Describe what cloud computing is and the role of the cloud in IT organizations
- List the key characteristics of cloud computing, delivery methods, and deployment models
- State the criteria that IT teams must evaluate to determine whether the cloud is the right solution for a particular IT service

---

# MODULE 1 – Introduction to Cloud Computing

## 1.1 What is Cloud Computing?

**Cloud computing** is the delivery of computing services — including servers, storage, databases, networking, software, analytics, and intelligence — over the Internet ("the cloud") to offer faster innovation, flexible resources, and economies of scale.

Rather than owning and maintaining physical data centers and servers, organizations can access technology services on an **as-needed basis** from a cloud provider and pay only for what they use.

### The NIST Definition

The U.S. National Institute of Standards and Technology (NIST) defines cloud computing as:

> *"A model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources (e.g., networks, servers, storage, applications, and services) that can be rapidly provisioned and released with minimal management effort or service provider interaction."*

This definition highlights five essential characteristics, three service models, and four deployment models — all covered in detail in the following sections.

---

## 1.2 A Brief History of Cloud Computing

Understanding how cloud computing evolved helps contextualize why it has become so dominant in enterprise IT.

| Era | Period | Key Development |
|---|---|---|
| **Mainframe era** | 1950s–1970s | Centralized computing with dumb terminals; users share mainframe resources |
| **Client-Server era** | 1980s–1990s | Distributed computing; PCs connected to local servers |
| **Internet era** | Late 1990s | Application Service Providers (ASPs) host software on the web |
| **Virtualization era** | Early 2000s | VMware introduces server virtualization; resource pooling becomes mainstream |
| **Cloud era** | 2006–present | AWS launches EC2 and S3 (2006); cloud becomes the standard IT delivery model |
| **Cloud-native era** | 2010s–present | Containers (Docker), Kubernetes, microservices, serverless — cloud becomes a development paradigm |

---

## 1.3 The Role of Cloud in IT Organizations

Cloud computing has fundamentally changed the role of IT within organizations:

### From CapEx to OpEx

| Traditional IT | Cloud IT |
|---|---|
| Large upfront hardware investment (CapEx) | Pay-as-you-go operational expense (OpEx) |
| Hardware refresh cycles every 3–5 years | Continuous access to latest infrastructure |
| Fixed capacity (often over-provisioned) | Elastic capacity matching actual demand |
| Long procurement cycles (weeks to months) | Self-service provisioning in minutes |
| IT manages physical infrastructure | IT focuses on business value and innovation |

### Business Drivers for Cloud Adoption

- **Speed to market**: provision infrastructure in minutes rather than weeks
- **Global reach**: deploy applications in multiple regions with a few clicks
- **Cost optimization**: eliminate idle infrastructure, pay only for what you use
- **Innovation**: instant access to emerging technologies (AI/ML, IoT, serverless)
- **Resilience**: built-in redundancy and disaster recovery capabilities
- **Focus on core business**: shift IT staff from infrastructure management to strategic projects

### Cloud Adoption Models

Organizations typically adopt cloud through one of these paths:

1. **Lift-and-Shift (Rehost)**: move existing applications to cloud VMs with minimal changes
2. **Replatform**: make minor cloud optimizations without changing the core architecture
3. **Refactor/Re-architect**: redesign applications to be cloud-native (microservices, containers)
4. **Rebuild**: rewrite the application from scratch using cloud-native technologies
5. **Replace (SaaS)**: abandon legacy applications and adopt SaaS alternatives
6. **Retire**: decommission applications no longer needed

---

# MODULE 2 – Key Characteristics of Cloud Computing

The NIST model identifies **five essential characteristics** that define a true cloud service. If a service does not exhibit all five, it may not be genuinely "cloud."

## 2.1 On-Demand Self-Service

Users can **provision computing resources automatically** without requiring human interaction from the service provider.

**What this means in practice:**
- A developer can spin up a virtual machine in 2 minutes via a web portal or API
- A data scientist can provision a GPU cluster for a machine learning job and terminate it when done
- Storage capacity can be increased instantly via a management console

**Why it matters:**
- Removes bottlenecks from IT procurement processes
- Empowers development and operations teams to move faster
- Enables experimentation at low cost

---

## 2.2 Broad Network Access

Cloud services are available over the network and accessible through **standard mechanisms** (web browsers, mobile apps, APIs) from a wide range of devices (laptops, tablets, smartphones, IoT devices).

**Key points:**
- Access via **public Internet** or **private connectivity** (VPN, Direct Connect, ExpressRoute)
- Standards-based APIs (REST, SOAP) ensure interoperability
- Location-independence: resources accessible from anywhere in the world
- Supports remote work, global teams, and mobile workforces

---

## 2.3 Resource Pooling

The provider's computing resources are **pooled to serve multiple consumers** using a multi-tenant model. Physical and virtual resources are dynamically assigned and reassigned based on demand.

**Multi-tenancy explained:**
- Multiple customers share the same physical infrastructure
- Each customer's data and workloads are **logically isolated**
- Customers generally do not control or know the exact physical location of the resources
- However, customers may specify region/country for regulatory compliance

**Benefits:**
- Economies of scale for the provider → lower costs for the customer
- Higher utilization of physical hardware
- Faster resource provisioning

**Concerns addressed:**
- Logical isolation (virtualization, containers)
- Encryption of data at rest and in transit
- Strong access control and identity management

---

## 2.4 Rapid Elasticity

Resources can be **elastically provisioned and released**, in some cases automatically, to scale rapidly outward and inward commensurate with demand.

**Two types of elasticity:**

| Type | Description | Example |
|---|---|---|
| **Horizontal scaling (scale-out)** | Add more instances/nodes | Add web servers during a traffic spike |
| **Vertical scaling (scale-up)** | Increase resources of existing instance | Upgrade VM from 4 to 16 vCPUs |

**Auto-scaling:**
- Cloud platforms (AWS Auto Scaling, Azure VMSS, GCP Managed Instance Groups) automatically adjust capacity based on policies (CPU%, memory, request rate)
- **Scale-out** when load increases → **scale-in** when load decreases

**Why it matters:**
- Never over-provision (pay for idle capacity)
- Never under-provision (suffer performance degradation)
- Handle unpredictable workloads gracefully (e-commerce peak seasons, viral events)

---

## 2.5 Measured Service

Cloud systems automatically **control and optimize resource use** by leveraging metering capabilities. Resource usage is monitored, controlled, and reported — providing transparency for both the provider and the consumer.

**What is measured:**
- Compute: vCPU hours, instance-hours
- Storage: GB stored, I/O operations (IOPS), data transfer
- Network: GB ingress/egress, number of API calls
- Managed services: requests processed, functions invoked, database query units

**Business implications:**
- **Pay-per-use billing**: granular cost attribution to projects, teams, departments
- **Cost optimization**: identify underutilized resources and right-size workloads
- **Budgeting and forecasting**: predict cloud spending based on usage trends
- **Showback/Chargeback**: allocate cloud costs to business units

**Tools:** AWS Cost Explorer, Azure Cost Management, GCP Billing, third-party tools (CloudHealth, Apptio)

---

# MODULE 3 – Cloud Service Delivery Models

Cloud services are delivered through three primary models, often visualized as a "responsibility stack."

## 3.1 The Shared Responsibility Model

Before diving into each model, it is essential to understand the **Shared Responsibility Model** — the division of security and management duties between the cloud provider and the customer.

```
TRADITIONAL IT       IaaS              PaaS              SaaS
─────────────────    ──────────────    ──────────────    ──────────────
Applications    ←── [Customer]    ←── [Customer]    ←── [Provider]
Data            ←── [Customer]    ←── [Customer]    ←── [Provider]
Runtime         ←── [Customer]    ←── [Provider]    ←── [Provider]
Middleware      ←── [Customer]    ←── [Provider]    ←── [Provider]
OS              ←── [Customer]    ←── [Provider]    ←── [Provider]
Virtualization  ←── [Customer]    ←── [Provider]    ←── [Provider]
Servers         ←── [Customer]    ←── [Provider]    ←── [Provider]
Storage         ←── [Customer]    ←── [Provider]    ←── [Provider]
Networking      ←── [Customer]    ←── [Provider]    ←── [Provider]
Data Center     ←── [Customer]    ←── [Provider]    ←── [Provider]
```

As you move from IaaS → PaaS → SaaS, **the provider manages more**, and the customer has **less control but less operational burden**.

---

## 3.2 IaaS – Infrastructure as a Service

### Definition

IaaS provides **virtualized computing infrastructure** over the Internet. The provider manages the physical hardware, hypervisor, networking, and storage hardware. The customer manages everything from the OS upward.

### What the Customer Controls

- Operating system (choice of Windows, Linux distributions, etc.)
- Installed software, middleware, runtimes
- Applications and application configuration
- Data stored on virtual disks
- Virtual network configuration (subnets, security groups, routing)

### Key IaaS Components

| Component | Description | AWS Example | Azure Example |
|---|---|---|---|
| **Virtual Machines** | Compute instances of various sizes | EC2 | Azure VM |
| **Block Storage** | Virtual disks attached to VMs | EBS | Managed Disks |
| **Object Storage** | Scalable unstructured data storage | S3 | Blob Storage |
| **Virtual Networking** | Software-defined networks, subnets, routing | VPC | VNet |
| **Load Balancers** | Distribute traffic across instances | ELB/ALB | Azure Load Balancer |
| **DNS** | Domain name resolution | Route 53 | Azure DNS |
| **CDN** | Content delivery network | CloudFront | Azure CDN |

### Advantages of IaaS

- **Maximum flexibility**: full control over OS and software stack
- **Lift-and-shift**: migrate existing applications with minimal changes
- **Familiar environment**: works like on-premises servers but virtualized
- **Cost savings**: eliminate hardware procurement and data center costs

### Disadvantages of IaaS

- **High management burden**: customer responsible for OS patching, security hardening, middleware configuration
- **Requires IT expertise**: not suitable for teams without infrastructure skills
- **Security responsibility**: misconfigured security groups or OS vulnerabilities are the customer's responsibility

### IaaS Use Cases

- Hosting web applications and APIs
- Development and testing environments
- High-performance computing (HPC) and batch processing
- Disaster recovery and backup infrastructure
- Big data processing clusters

---

## 3.3 PaaS – Platform as a Service

### Definition

PaaS provides a **managed platform** on which developers can build, run, and manage applications without dealing with the complexity of the underlying infrastructure. The provider manages everything from the OS to the runtime environment.

### What the Customer Controls

- Application code and logic
- Application configuration and settings
- Data
- (Sometimes) runtime version selection

### Key PaaS Components

| Component | Description | AWS Example | Azure Example |
|---|---|---|---|
| **App Platform** | Managed runtime for web apps | Elastic Beanstalk | Azure App Service |
| **Managed Databases** | Fully managed relational/NoSQL DB | RDS, DynamoDB | Azure SQL, Cosmos DB |
| **Message Queues** | Async messaging between services | SQS, SNS | Azure Service Bus |
| **API Gateway** | Manage, secure, and scale APIs | API Gateway | Azure API Management |
| **CI/CD Pipeline** | Automated build, test, and deploy | CodePipeline | Azure DevOps |
| **Data Analytics** | Managed big data processing | EMR, Glue | Azure HDInsight, Synapse |
| **AI/ML Platform** | Managed machine learning services | SageMaker | Azure ML |

### Advantages of PaaS

- **Developer productivity**: focus on writing code, not managing servers
- **Faster time to market**: pre-configured environments reduce setup time
- **Built-in scalability**: platform automatically scales with demand
- **Reduced operational overhead**: provider handles OS updates, patches, security
- **Managed services**: databases, caching, queuing — all fully managed

### Disadvantages of PaaS

- **Less control**: cannot customize the OS or runtime environment deeply
- **Vendor lock-in risk**: proprietary PaaS APIs may be hard to migrate away from
- **Limited flexibility**: may not support all languages, frameworks, or custom dependencies
- **"Works on my machine" problem**: platform constraints may differ from local dev environments

### PaaS Use Cases

- Web and mobile application backends
- Microservices architectures
- Data pipelines and analytics workloads
- API development and management
- Rapid prototyping and startup environments

---

## 3.4 SaaS – Software as a Service

### Definition

SaaS delivers **complete, ready-to-use applications** over the Internet. The provider manages everything — from the data center to the application itself. The customer simply uses the software through a web browser or app.

### What the Customer Controls

- User accounts and permissions
- Business data entered into the application
- Application configuration options (within what the provider allows)

### Common SaaS Categories

| Category | Examples |
|---|---|
| **Productivity & Collaboration** | Microsoft 365, Google Workspace, Slack, Zoom |
| **CRM (Customer Relationship Management)** | Salesforce, HubSpot, Microsoft Dynamics |
| **ERP (Enterprise Resource Planning)** | SAP S/4HANA Cloud, Oracle ERP Cloud, NetSuite |
| **ITSM (IT Service Management)** | ServiceNow, JIRA Service Management |
| **HR & Payroll** | Workday, BambooHR, ADP |
| **Security** | Okta, CrowdStrike, Zscaler |
| **Storage & Backup** | Dropbox, Box, Backblaze |
| **Development Tools** | GitHub, GitLab, CircleCI |

### Advantages of SaaS

- **Zero infrastructure management**: no servers, no OS, no updates to manage
- **Instant access**: available immediately, accessible from any device
- **Automatic updates**: provider handles all software updates and patches
- **Lower upfront cost**: subscription-based pricing (per user/month)
- **Accessibility**: any internet-connected device can access the service

### Disadvantages of SaaS

- **Minimal customization**: limited ability to modify the software
- **Data sovereignty concerns**: data stored on provider infrastructure
- **Internet dependency**: service unavailable if Internet connection drops
- **Subscription cost creep**: costs can accumulate with many SaaS subscriptions (SaaS sprawl)
- **Integration complexity**: connecting multiple SaaS tools may require middleware (iPaaS)

### SaaS Use Cases

- Company-wide productivity tools (email, calendar, documents)
- Sales and marketing automation
- HR management and payroll
- Customer support and ticketing

---

## 3.5 Extended Service Models

Beyond the classic three, the industry has introduced additional "aaS" models:

| Model | Full Name | Description | Examples |
|---|---|---|---|
| **FaaS** | Function as a Service | Run individual code functions in response to events; no server management | AWS Lambda, Azure Functions, GCP Cloud Functions |
| **CaaS** | Container as a Service | Managed container orchestration | AWS ECS/EKS, Azure AKS, GCP GKE |
| **DBaaS** | Database as a Service | Fully managed databases | AWS RDS, Azure Cosmos DB, MongoDB Atlas |
| **STaaS** | Storage as a Service | Cloud-based storage provisioning | AWS S3, Azure Blob, NetApp Cloud Volumes ONTAP |
| **DRaaS** | Disaster Recovery as a Service | Cloud-based disaster recovery | Zerto, Veeam Cloud, AWS Elastic Disaster Recovery |
| **SECaaS** | Security as a Service | Cloud-delivered security services | Zscaler, Okta, Cloudflare |
| **AIaaS** | AI as a Service | Pre-built AI/ML models and APIs | AWS Rekognition, Azure Cognitive Services, Google Vision AI |
| **NaaS** | Network as a Service | Virtualized network services | AWS Transit Gateway, SD-WAN as a service |

---

# MODULE 4 – Cloud Deployment Models

## 4.1 Public Cloud

### Definition

A **public cloud** is a cloud infrastructure **owned and operated by a third-party provider** and made available to the general public or large industry groups over the Internet. Resources are shared among multiple customers (multi-tenant).

### Characteristics

- Infrastructure owned entirely by the provider (AWS, Azure, GCP, etc.)
- Massive economies of scale → lowest cost per unit of compute/storage
- Highly available and globally distributed (multiple Availability Zones and Regions)
- Customers access resources via the Internet or dedicated connections
- Provider responsible for physical security, hardware maintenance, and global network

### Key Concepts: Regions and Availability Zones

**Region**: a geographic area containing multiple Availability Zones (e.g., us-east-1, eu-west-1)

**Availability Zone (AZ)**: one or more discrete data centers within a Region, with independent power, cooling, and networking. AZs within a Region are connected by low-latency links.

```
Region: US East (N. Virginia)
├── Availability Zone: us-east-1a (Data Center 1, Data Center 2)
├── Availability Zone: us-east-1b (Data Center 3, Data Center 4)
├── Availability Zone: us-east-1c (Data Center 5)
└── Availability Zone: us-east-1d (Data Center 6)
```

Deploying across multiple AZs ensures **high availability**: if one AZ fails, the others continue serving traffic.

### Advantages

- No CapEx investment in hardware
- Access to a vast catalog of services (200+ in AWS)
- Global presence in minutes
- Automatic hardware refresh (access to latest CPUs, GPUs, SSDs)
- Built-in redundancy and DR capabilities

### Disadvantages

- **Security/privacy concerns**: data stored on shared infrastructure
- **Compliance challenges**: data residency requirements may restrict use
- **Internet dependency**: requires reliable Internet connectivity
- **Vendor lock-in**: proprietary services create migration barriers
- **Cost unpredictability**: poorly managed cloud can become very expensive

---

## 4.2 Private Cloud

### Definition

A **private cloud** is cloud infrastructure **dedicated exclusively to a single organization**, either managed internally or by a third-party provider, hosted on-premises or in a colocation facility.

### Characteristics

- Resources are **not shared** with other organizations
- Organization has full control over hardware, software, and data
- Can implement custom security policies and compliance controls
- Higher upfront costs but more predictable long-term costs

### Types of Private Cloud

| Type | Description | Examples |
|---|---|---|
| **On-premises private cloud** | Infrastructure in the organization's own data center | VMware Cloud Foundation, OpenStack |
| **Hosted private cloud** | Dedicated infrastructure in a provider's data center | IBM Cloud Dedicated, Rackspace Private Cloud |
| **Managed private cloud** | Organization owns hardware, third party manages it | NetApp Managed Private Cloud |

### Private Cloud Technologies

- **Hypervisors**: VMware vSphere, Microsoft Hyper-V, KVM
- **Cloud management platforms**: VMware vRealize, OpenStack, Microsoft Azure Stack
- **Software-defined networking**: VMware NSX, Cisco ACI
- **Software-defined storage**: NetApp ONTAP, VMware vSAN, Ceph

### Advantages

- **Maximum security and control**: full governance over data and infrastructure
- **Customization**: tailor hardware and software to specific requirements
- **Compliance**: easier to meet strict regulatory requirements (GDPR, HIPAA, PCI-DSS)
- **Predictable performance**: no "noisy neighbor" problems from shared infrastructure
- **Data sovereignty**: data never leaves the organization's control

### Disadvantages

- **High CapEx**: significant upfront investment in hardware
- **Limited scalability**: constrained by physical hardware purchased
- **IT overhead**: requires in-house expertise to manage
- **Slower innovation**: no automatic access to provider's new services
- **Under-utilization risk**: hardware may sit idle if demand is lower than planned

### When to Choose Private Cloud

- Highly regulated industries (banking, healthcare, government, defense)
- Organizations with large, predictable, stable workloads
- Strict data sovereignty requirements
- Specialized hardware needs (custom GPUs, FPGAs, mainframes)
- Legacy applications that cannot be easily migrated

---

## 4.3 Hybrid Cloud

### Definition

A **hybrid cloud** combines **public and private cloud** (and optionally on-premises infrastructure) with **orchestration between them**, allowing data and applications to move between environments.

### Architecture

```
On-Premises / Private Cloud          Public Cloud
─────────────────────────            ─────────────────
  Sensitive workloads                  Elastic workloads
  Legacy applications      ←──────→   Dev/Test environments
  Regulated data                       Burst capacity
  Core databases                       Global CDN
─────────────────────────            ─────────────────
         │                                   │
         └─────── Secure Connection ─────────┘
                  (VPN / Direct Connect /
                   ExpressRoute / SD-WAN)
```

### Key Hybrid Cloud Concepts

**Cloud Bursting**: when on-premises resources reach capacity, overflow workloads automatically burst to the public cloud. When demand decreases, workloads return on-premises.

**Data tiering**: frequently accessed ("hot") data stays on-premises for performance; rarely accessed ("cold") data is automatically moved to low-cost cloud storage.

**Unified management plane**: a single pane of glass to manage resources across both environments (e.g., VMware Cloud on AWS, Azure Arc, Google Anthos, NetApp BlueXP).

### Connectivity Options

| Method | Description | Bandwidth | Latency | Security |
|---|---|---|---|---|
| **IPsec VPN** | Encrypted tunnel over public Internet | Variable | Higher | Good |
| **AWS Direct Connect** | Dedicated private circuit to AWS | 1–100 Gbps | Low | High |
| **Azure ExpressRoute** | Dedicated private circuit to Azure | 50 Mbps–100 Gbps | Low | High |
| **GCP Cloud Interconnect** | Dedicated private circuit to GCP | 10–100 Gbps | Low | High |
| **SD-WAN** | Software-defined overlay network | Variable | Low-Medium | Good |

### Advantages

- **Flexibility**: place each workload in the most appropriate environment
- **Cost optimization**: keep steady-state workloads on-premises (where cheaper at scale), burst to cloud for peaks
- **Risk mitigation**: gradual cloud migration, not all-or-nothing
- **Compliance**: sensitive data stays on-premises, non-sensitive data in cloud
- **Business continuity**: cloud as DR target for on-premises systems

### Disadvantages

- **Complexity**: managing two or more environments requires significant expertise
- **Network dependency**: performance depends on the quality of connectivity between environments
- **Security challenges**: more attack surface, data in transit between environments
- **Cost management difficulty**: spending spread across on-premises and cloud

---

## 4.4 Multi-Cloud

### Definition

**Multi-cloud** refers to the use of **cloud services from multiple providers** simultaneously. An organization might use AWS for compute, Azure for identity and Office 365 integration, and GCP for machine learning — all at the same time.

### Multi-Cloud vs Hybrid Cloud

| Aspect | Hybrid Cloud | Multi-Cloud |
|---|---|---|
| Definition | On-premises + public cloud | Multiple public cloud providers |
| Integration | Tight (orchestrated) | Loose (often independent) |
| Goal | Extend on-premises to cloud | Avoid lock-in, best-of-breed |
| Complexity | Medium | High |

### Why Organizations Choose Multi-Cloud

- **Avoid vendor lock-in**: not dependent on a single provider's pricing or SLAs
- **Best-of-breed services**: use GCP's BigQuery for analytics, AWS for compute, Azure for identity
- **Geographic coverage**: some providers have better coverage in specific regions
- **Redundancy**: if one provider has an outage, workloads run on another
- **Regulatory requirements**: some countries require data to be in clouds operated locally

### Multi-Cloud Challenges

- **Management complexity**: different consoles, APIs, pricing models, and support structures
- **Data transfer costs**: moving data between clouds incurs egress fees
- **Security consistency**: different security models and tools for each provider
- **Skill requirements**: staff must know multiple platforms
- **Governance**: consistent policy enforcement across clouds

### Multi-Cloud Management Tools

- **HashiCorp Terraform**: infrastructure-as-code for multiple cloud providers
- **Kubernetes**: container orchestration that runs on any cloud
- **NetApp BlueXP**: unified data management across clouds
- **VMware Tanzu / Google Anthos / Azure Arc**: hybrid and multi-cloud application platforms

---

## 4.5 Community Cloud

A less common but important model: a **community cloud** is shared by several organizations with **common concerns** (security requirements, compliance standards, mission objectives).

**Examples:**
- Government cloud (shared by multiple government agencies)
- Healthcare cloud (shared by hospitals and healthcare providers meeting HIPAA)
- Financial services cloud (shared by banks with common PCI-DSS requirements)

**Examples of community clouds:** AWS GovCloud, Microsoft Azure Government, Google Cloud for Public Sector

---

# MODULE 5 – Cloud Evaluation Criteria

Before adopting cloud services, IT teams must carefully evaluate several key criteria. These factors determine whether cloud is the right choice for a specific workload or service — and which deployment model is most appropriate.

## 5.1 Security

Security is consistently cited as the **top concern** for organizations considering cloud adoption.

### Security Considerations

#### Identity and Access Management (IAM)
- Who has access to cloud resources?
- Are least-privilege principles enforced?
- Is multi-factor authentication (MFA) required?
- How are service accounts and API keys managed?

**Key IAM concepts:**
- **IAM Roles**: define permissions for users, groups, and services
- **Federation**: integrate with existing corporate identity providers (Active Directory, Okta) via SAML or OIDC
- **Privileged Access Management (PAM)**: extra controls for administrative accounts

#### Network Security
- **Virtual Private Cloud (VPC)**: isolated network environment within the public cloud
- **Security Groups / Network ACLs**: stateful and stateless packet filtering
- **Private Endpoints**: connect to cloud services without traversing the public Internet
- **DDoS Protection**: AWS Shield, Azure DDoS Protection, GCP Cloud Armor

#### Data Security
- **Encryption at-rest**: AES-256 for stored data; customer-managed keys (BYOK) or provider-managed keys
- **Encryption in-transit**: TLS 1.2/1.3 for all data moving over networks
- **Key Management**: AWS KMS, Azure Key Vault, GCP Cloud KMS — hardware security modules (HSM)
- **Data Loss Prevention (DLP)**: detect and prevent sensitive data from leaving the environment

#### Security Monitoring
- **SIEM (Security Information and Event Management)**: centralized log analysis and threat detection
- **CSPM (Cloud Security Posture Management)**: continuously scan for misconfigurations
- **CWPP (Cloud Workload Protection Platform)**: protect running workloads from threats
- Tools: AWS Security Hub, Azure Defender, GCP Security Command Center, Prisma Cloud

#### The Shared Responsibility Model and Security

A critical concept: **security in the cloud is shared**. The provider secures the infrastructure; the customer secures their configurations, data, and access controls.

| Provider Responsibility | Customer Responsibility |
|---|---|
| Physical data center security | Identity and access management |
| Hardware and hypervisor security | OS patching (IaaS) |
| Global network security | Application security |
| Service availability | Data encryption (choice) |
| Compliance of infrastructure | Firewall/security group configuration |

**Most cloud security breaches are caused by customer misconfiguration — not provider vulnerabilities.**

---

## 5.2 Privacy

Privacy concerns center on **who has access to data** stored in the cloud and under what circumstances.

### Key Privacy Questions

- Does the cloud provider have access to customer data?
- Can data be accessed by the provider's employees for support purposes?
- Does the provider use customer data to train AI models or for other purposes?
- Are data access logs maintained and auditable?

### Data Residency and Sovereignty

**Data residency**: the physical or geographic location where data is stored.

**Data sovereignty**: the concept that data is subject to the laws of the country in which it is stored.

**Why this matters:**
- The USA CLOUD Act (2018) allows US authorities to request data from US companies regardless of where it is physically stored
- GDPR requires personal data of EU citizens to be processed according to EU rules
- Some countries (Russia, China) require local data storage

**Addressing data residency:**
- Choose cloud **Regions** in the required country or jurisdiction
- Use **Data Residency Guarantees** offered by providers (AWS Data Residency, Azure Sovereign Regions)
- Consider **sovereign cloud** options (Azure for Government, French Sovereign Cloud, etc.)

### Privacy Regulations Relevant to Cloud

| Regulation | Jurisdiction | Key Privacy Requirements |
|---|---|---|
| **GDPR** | EU/EEA | Lawful basis for processing, consent, data subject rights, DPO, breach notification |
| **CCPA/CPRA** | California, USA | Right to know, right to delete, opt-out of data selling |
| **LGPD** | Brazil | Similar to GDPR, data processing principles and subject rights |
| **PDPA** | Singapore/Thailand | Consent requirements, data protection officer |
| **PIPEDA** | Canada | Privacy in commercial activities, consent, access rights |

---

## 5.3 Compliance

**Compliance** ensures that cloud usage meets the legal, regulatory, and contractual requirements applicable to an organization's industry and geography.

### Cloud Compliance Framework

```
Regulatory Requirements
        │
        ▼
Cloud Provider Certifications ──→ Does the provider meet baseline compliance?
        │
        ▼
Customer Configuration ──────────→ Does the customer configure services correctly?
        │
        ▼
Ongoing Monitoring ──────────────→ Is compliance continuously maintained?
```

### Major Compliance Standards and Certifications

| Standard | Industry | What It Covers |
|---|---|---|
| **ISO 27001** | All | Information Security Management System (ISMS) |
| **SOC 2 Type II** | All (USA) | Security, availability, processing integrity, confidentiality, privacy |
| **PCI-DSS** | Finance / Retail | Credit card data protection |
| **HIPAA** | Healthcare (USA) | Protected Health Information (PHI) |
| **FedRAMP** | US Government | Federal cloud security authorization |
| **GDPR** | EU | Personal data processing |
| **CSA STAR** | Cloud | Cloud-specific security certification |
| **FIPS 140-2** | Government / Defense | Cryptographic module standards |

### How Cloud Providers Support Compliance

- **Compliance Marketplaces**: AWS Artifact, Azure Compliance Manager, GCP Compliance Reports
- **Pre-built compliance controls**: managed services often have compliance controls built-in
- **Audit support**: access to logs, certifications, and third-party audit reports
- **BAA (Business Associate Agreements)**: for HIPAA-covered entities
- **DPA (Data Processing Agreements)**: for GDPR data processors

### Compliance ≠ Security

An important distinction: meeting compliance requirements **does not automatically mean the environment is secure**. Compliance defines a minimum baseline; security requires ongoing active management above and beyond compliance checkboxes.

---

## 5.4 Performance

Cloud performance must be evaluated carefully to ensure that business requirements — response times, throughput, availability — can be met.

### Key Performance Factors

#### Latency
- **Network latency**: time for data to travel between user and cloud (affected by geographic distance)
- **Storage latency**: time to read/write data (NVMe SSD < SAS SSD < HDD)
- **Application latency**: end-to-end response time experienced by users

**Reducing latency:**
- Choose cloud **Regions** geographically close to users
- Use **CDN (Content Delivery Network)** to cache content near users
- Use **Direct Connect / ExpressRoute** for predictable low-latency private connectivity
- Deploy at the **Edge** for ultra-low latency IoT/real-time applications

#### Throughput and Bandwidth
- **Network bandwidth**: available between cloud and on-premises (or between services)
- **Storage throughput**: MB/s for sequential read/write operations
- **IOPS**: random I/O operations per second (critical for databases)

#### The "Noisy Neighbor" Problem
In shared cloud environments, other tenants consuming excessive resources can degrade performance for neighboring workloads.

**Mitigations:**
- Use **dedicated instances** (AWS Dedicated Instances, Azure Isolated VMs)
- Use **premium storage tiers** with guaranteed IOPS
- Choose **reserved or bare-metal instances** for consistent performance

#### Performance Benchmarking
Before migration, IT teams should:
1. Baseline current on-premises performance metrics
2. Test equivalent cloud configurations
3. Compare results and identify gaps
4. Right-size cloud resources based on actual performance requirements (not assumed)

#### SLA (Service Level Agreement)
Cloud providers publish **SLAs** that guarantee minimum availability and performance:

| Service | AWS SLA | Azure SLA | GCP SLA |
|---|---|---|---|
| Virtual Machines | 99.99% (multi-AZ) | 99.99% (Availability Set) | 99.99% (multi-zone) |
| Object Storage (S3/Blob) | 99.9% | 99.9% | 99.9% |
| Managed Databases (RDS) | 99.95% | 99.99% | 99.95% |

**Important**: SLAs define **uptime guarantees and compensation (service credits)** — not a guarantee of zero downtime.

---

## 5.5 Vendor Lock-In

**Vendor lock-in** occurs when an organization becomes so dependent on a single cloud provider's proprietary services that **switching providers becomes prohibitively expensive, complex, or risky**.

### Types of Lock-In

| Type | Description | Example |
|---|---|---|
| **Service lock-in** | Heavy use of proprietary managed services | AWS DynamoDB, Azure CosmosDB |
| **Data lock-in** | Difficulty and cost of moving large datasets | High egress fees, proprietary data formats |
| **Skills lock-in** | Team expertise built only on one platform | AWS-only certified engineers |
| **Contract lock-in** | Multi-year committed-use contracts | Reserved Instances, Committed Use Discounts |
| **Operational lock-in** | Processes, monitoring, and tooling built for one provider | CloudWatch-only monitoring pipelines |

### Strategies to Minimize Lock-In

**Architectural strategies:**
- Design applications using **open standards** (REST APIs, SQL, Kubernetes, S3-compatible APIs)
- Use **containerization (Docker/Kubernetes)** — containers run on any cloud
- Prefer **open-source databases** (PostgreSQL, MySQL) over proprietary (Aurora, Cosmos DB)
- Use **multi-cloud storage layers** — NetApp Cloud Volumes ONTAP abstracts storage across AWS, Azure, and GCP

**Operational strategies:**
- Use **Terraform** (infrastructure-as-code) for cloud-agnostic provisioning
- Invest in **multi-cloud skills** rather than single-provider certifications only
- Maintain **cloud exit plans**: know what it would take to migrate to another provider
- **Monitor and audit** proprietary service usage regularly

**Commercial strategies:**
- Negotiate **shorter contract terms** to retain flexibility
- Avoid locking in large committed-use discounts without understanding migration costs
- Use **spot/preemptible pricing** rather than long-term reserved instances where possible

### Lock-In Acceptance vs Avoidance

In practice, **some degree of lock-in is acceptable** in exchange for the productivity and efficiency gains of managed services. The key is making **conscious, informed decisions** rather than accidentally accumulating lock-in.

```
Low Lock-In Risk         │    High Lock-In Risk
─────────────────────────┼──────────────────────────────
Standard VMs (IaaS)      │    Proprietary DB engines
Open-source databases    │    Proprietary ML platforms
Kubernetes (containers)  │    Provider-specific serverless
S3-compatible object API │    Proprietary data formats
Terraform (IaC)          │    Provider-native IaC only
```

---

## 5.6 Additional Evaluation Criteria

Beyond the core five areas above, IT teams should evaluate:

### Total Cost of Ownership (TCO)

Cloud appears cheaper at first glance (no CapEx), but TCO analysis must include:

| Cost Category | On-Premises | Cloud |
|---|---|---|
| Hardware purchase | CapEx (every 3–5 years) | Included in service cost |
| Data center space/power | Significant OpEx | Included |
| IT staff for infrastructure | High | Reduced (but not zero) |
| Compute/storage costs | Fixed (regardless of use) | Variable (per use) |
| Egress/data transfer | Low (internal network) | Can be significant |
| Software licenses | Separate purchase | Often included (SaaS) |
| Disaster recovery | Separate investment | Often built-in |

**Key insight**: cloud can be cheaper for **variable or unpredictable workloads**, but on-premises can be cheaper for **large, stable, predictable workloads** at scale.

### Availability and Reliability

- What is the provider's **SLA** for each service?
- Are multiple **Availability Zones** used for redundancy?
- Is the provider's **track record** for outages acceptable?
- What is the recovery procedure if the cloud service goes down?

### Interoperability

- Can the cloud service integrate with existing on-premises systems?
- Does it support standard protocols and APIs?
- How does it handle data synchronization between cloud and on-premises?
- Does it work with existing identity management systems?

### Support and Service Management

- What level of support does the provider offer (basic, business, enterprise)?
- What is the expected response time for critical incidents?
- Does the provider offer **professional services** for migration assistance?
- Is there a **partner ecosystem** for additional support and services?

---

# MODULE 6 – Cloud Decision Framework

## 6.1 Cloud Readiness Assessment

Before adopting cloud, organizations should conduct a **Cloud Readiness Assessment** to evaluate:

| Dimension | Key Questions |
|---|---|
| **Technical readiness** | Are applications cloud-compatible? Are there dependencies on on-prem-only resources? |
| **Skills readiness** | Does the team have cloud skills? What training is needed? |
| **Process readiness** | Are DevOps/Agile practices in place? Are governance processes cloud-aware? |
| **Financial readiness** | Is the budget model flexible enough to support OpEx-based spending? |
| **Security readiness** | Are security policies updated for cloud environments? |
| **Compliance readiness** | Have all regulatory requirements been mapped to cloud controls? |

## 6.2 Workload Classification

Not every workload is equally suited for cloud. IT teams should classify workloads across multiple dimensions:

| Workload Type | Cloud Suitability | Rationale |
|---|---|---|
| Variable/seasonal workloads | ★★★★★ | Elasticity eliminates over-provisioning |
| New cloud-native applications | ★★★★★ | Built for cloud from day one |
| Dev/Test environments | ★★★★★ | Start/stop on demand, no idle costs |
| Batch processing / HPC | ★★★★☆ | On-demand large compute, pay only during jobs |
| Web applications | ★★★★☆ | Global reach, CDN, auto-scaling |
| Collaboration tools | ★★★★☆ | SaaS alternatives often superior |
| Legacy monolithic applications | ★★☆☆☆ | Hard to cloud-optimize without refactoring |
| Latency-sensitive applications | ★★☆☆☆ | May require edge deployment or private cloud |
| Highly regulated data | ★★☆☆☆ | Private or sovereign cloud may be required |
| Mainframe workloads | ★☆☆☆☆ | Very difficult to migrate; typically remain on-premises |

## 6.3 Cloud Selection Checklist

When evaluating cloud providers and deployment models, use this structured checklist:

**Security & Compliance**
- [ ] Provider holds required compliance certifications (SOC2, ISO 27001, PCI-DSS, etc.)
- [ ] Data encryption at rest and in transit is supported
- [ ] Customer-managed encryption keys (BYOK) are available
- [ ] IAM with MFA and RBAC is available
- [ ] Audit logging and SIEM integration are available

**Privacy & Data Residency**
- [ ] Data can be stored in the required geographic region
- [ ] Data processing agreements (DPA/BAA) are available
- [ ] Provider access to customer data is contractually restricted
- [ ] Data portability and export are supported

**Performance**
- [ ] Published SLA meets business requirements
- [ ] Network latency to target users is acceptable
- [ ] Storage IOPS and throughput meet application requirements
- [ ] Benchmark tests validate performance claims

**Vendor Lock-In**
- [ ] Open standards and APIs are used where possible
- [ ] A documented exit/migration plan exists
- [ ] Data portability is tested (can you export your data?)
- [ ] Dependencies on proprietary services are documented and accepted

**Cost**
- [ ] TCO analysis completed (cloud vs on-premises)
- [ ] Cost monitoring and budget alerting are configured
- [ ] Reserved/committed pricing is evaluated for stable workloads
- [ ] Data egress costs are modeled

---

# Summary – Conceptual Map

```
CLOUD COMPUTING FUNDAMENTALS
│
├── 📖 WHAT IS CLOUD COMPUTING?
│   ├── On-demand delivery of IT services over the Internet
│   ├── History: Mainframe → Client-Server → Internet → Virtualization → Cloud → Cloud-Native
│   └── Role: CapEx→OpEx, speed, innovation, global reach
│
├── 🔑 5 ESSENTIAL CHARACTERISTICS (NIST)
│   ├── 1. On-Demand Self-Service (no human provider interaction)
│   ├── 2. Broad Network Access (any device, anywhere)
│   ├── 3. Resource Pooling (multi-tenancy, shared infrastructure)
│   ├── 4. Rapid Elasticity (auto scale-out and scale-in)
│   └── 5. Measured Service (pay-per-use metering)
│
├── 🏗️ SERVICE DELIVERY MODELS
│   ├── IaaS: VMs, storage, networking → customer manages OS+
│   ├── PaaS: runtime, DB, middleware → customer manages app+data
│   ├── SaaS: complete application → customer manages data only
│   └── Extended: FaaS · CaaS · DBaaS · DRaaS · SECaaS · AIaaS
│
├── ☁️ DEPLOYMENT MODELS
│   ├── Public Cloud: shared, provider-owned (AWS, Azure, GCP)
│   ├── Private Cloud: dedicated, full control (VMware, OpenStack)
│   ├── Hybrid Cloud: on-prem + public, orchestrated (bursting, tiering)
│   ├── Multi-Cloud: multiple providers (lock-in avoidance, best-of-breed)
│   └── Community Cloud: shared by orgs with common requirements
│
└── ✅ EVALUATION CRITERIA
    ├── Security: IAM · Encryption · CSPM · Shared Responsibility
    ├── Privacy: Data residency · Sovereignty · GDPR · CCPA
    ├── Compliance: ISO 27001 · SOC2 · PCI-DSS · HIPAA · FedRAMP
    ├── Performance: Latency · IOPS · SLA · Noisy neighbor · Benchmarking
    ├── Vendor Lock-In: Service/Data/Skills lock-in · Mitigation strategies
    └── Additional: TCO · Availability · Interoperability · Support
```

---

*Documentation generated from NetApp course SALES-WBT-CLCMFUND – Industry Essentials: Cloud Computing*
*Extended detailed version – English*
