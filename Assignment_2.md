# Assignment 2: Database and Storage Systems Architecture Comparison

---

## 1. Relational Databases (RDBMS)

Relational Database Management Systems (RDBMS) primarily store data in structured, interlinked tables. They enforce strict schemas and rely on relations (such as foreign keys) to maintain consistency. Most real-world applications use relational systems where transactional integrity (ACID compliance) and data correctness are non-negotiable.

### Available Options
* **MySQL:** A highly popular, open-source relational database optimized for simple reads, fast queries, and straightforward transactional workloads.
* **PostgreSQL:** An advanced, open-source object-relational database built for complex operations, heavy computational workloads, and analytics-style queries.
* **Microsoft SQL Server:** A commercial, enterprise-grade RDBMS deeply integrated into the .NET and Microsoft Azure ecosystems.
* **Oracle Database:** A highly scalable, premium commercial enterprise database providing robust support and advanced management features for large-scale operations.

### Selection Criteria: When to Choose Which?

#### 1. Data Complexity and Query Sophistication
* **Choose MySQL** for simple data structures with low complexity, such as basic content management platforms where workloads consist primarily of straightforward CRUD operations.
* **Choose PostgreSQL** when your schema contains deeply nested relationships (e.g., Users -> Orders -> Items -> Payments) or requires intensive analytical querying. 

> **Why PostgreSQL excels at complex data:** It provides superior query optimization for advanced JOINs, window functions, Common Table Expressions (CTEs/WITH queries), complex subqueries, and native support for flexible data types like JSONB arrays and custom types.

#### 2. Read-Heavy vs. Write-Heavy Workloads
* **Choose MySQL** for highly read-heavy, simpler applications (e.g., news portals) where read-scaling and caching can be easily configured.
* **Choose PostgreSQL** for balanced or write-heavy applications requiring complex concurrent writes and simultaneous analytical processing without degrading performance.

#### 3. Budget and Licensing Constraints
* **Choose PostgreSQL or MySQL** for startups and cost-sensitive projects requiring free, open-source tools with strong community backing.
* **Choose Oracle or SQL Server** for enterprise environments with dedicated budgets that mandate commercial compliance, proprietary tooling, and contractual support legalities.

#### 4. Data Correctness and Criticality
* **Choose PostgreSQL / SQL Server / Oracle** when there is zero tolerance for data anomalies (e.g., financial ledger transfers). If a transaction partially fails, the database must reliably roll back the entire operations.
* **Choose MySQL** when minor inconsistencies carry low risk, such as recording social media likes or click logs, where absolute correctness takes a back seat to raw performance.

#### 5. Ecosystem Alignment
* **Choose Microsoft SQL Server** if your core backend is written in .NET and hosted on Microsoft Azure, leveraging native drivers and tooling.
* **Choose PostgreSQL or MySQL** if your stack relies on open-source technologies like Node.js, Python, or Go deployed on Linux-based infrastructure.

### Real-World Scenarios

#### Scenario A: Simple Blog Website
* **Data Models:** `users`, `posts`, `comments`.
* **Common Queries:** Fetching recent posts, displaying associated comments under a specific post ID.
* **Decision:** **MySQL**. The data relationships are flat, queries are simple, and read operations drastically outnumber write operations.

#### Scenario B: Ride-Sharing Application
* **Data Models:** `users`, `rides`, `geospatial_locations`, `pricing_logic`, `surge_calculation`.
* **Common Queries:** Dynamic surge pricing evaluation based on local demand matrices, location-based geometric filtering, and real-time backend analytics.
* **Decision:** **PostgreSQL**. The business logic demands advanced spatial queries (via PostGIS), complex mathematical window functions, and heavy concurrent transactional processing.

---

## 2. NoSQL Databases

NoSQL databases are designed to handle massive horizontal scale, flexible structural definitions (schema-less), and high read/write throughput by intentionally eliminating complex, performance-heavy JOIN operations through data denormalization.

### Available Options & Fundamental Data Patterns

The choice of a NoSQL database depends almost entirely on the underlying data access patterns and structural requirements:

| NoSQL Database Category | Key Technology Examples | Fundamental Data Pattern | Primary Focus |
| :--- | :--- | :--- | :--- |
| **Document Store** | MongoDB, Couchbase | **JSON-like Documents** hierarchical data structures. | Schema flexibility, rapid iteration, application-object mapping. |
| **Wide-Column / Column-Family** | Apache Cassandra, ScyllaDB | **Rows with dynamic columns**, distributed across partitions. | Massive scale, high-velocity writes, and high availability across data centers. |
| **Graph Database** | Neo4j, Amazon Neptune | **Nodes, Edges, and Properties** representing deeply interconnected entities. | Complex relationship traversal, fraud detection networks, and social graphs. |
| **Key-Value Store** | Redis, DynamoDB (Core) | **Opaque keys mapped to specific values** or structures. | Sub-millisecond lookup times for simple, non-relational records. |

### Real-World Scenario
Consider a **Global IoT Fleet Logging System** receiving telemetry data from millions of vehicles every second. The data pattern is purely write-heavy and structured chronologically. 

* **Decision:** **Apache Cassandra (Wide-Column)**. A traditional relational database would bottleneck on concurrent disk writes, and a graph database would be structurally irrelevant. Cassandra's wide-column data pattern scales linearly across multiple server nodes to handle endless append-only streaming writes.

---

## 3. Cloud Data Warehouses

Cloud data warehouses are optimized specifically for Online Analytical Processing (OLAP) rather than Online Transactional Processing (OLTP). Instead of fetching isolated individual rows, they scan petabytes of data to calculate aggregated corporate insights.

* **OLTP (Operational Database - e.g., PostgreSQL):** Optimized for `SELECT * FROM users WHERE id = 1;`
* **OLAP (Data Warehouse):** Optimized for `SELECT city, SUM(revenue) FROM orders GROUP BY city;`

### Architectural and Pricing Comparison

The primary operational drivers when choosing between major cloud data warehouses are how they decouple compute from storage and how they charge for usage:

| Feature | Snowflake | Google BigQuery | Amazon Redshift |
| :--- | :--- | :--- | :--- |
| **Architectural Model** | **Decoupled:** Compute and storage are independent layer options. Compute units spin down automatically when idle. | **Serverless:** Fully managed, abstracted execution layer. No infrastructure to provision or scale. | **Cluster-Based:** Provisioned nodes containing coupled or managed compute/storage slices. |
| **Pricing Model** | **Per-Second Compute + Storage:** Charged for data stored plus virtual warehouse run-time sizing. | **Per-Query / Slot Reservation:** Charged based on the volume of data scanned by queries (e.g., $5 per TB scanned). | **Time-Based Provisioning:** Upfront hourly fee based on the size and active status of the running cluster nodes. |
| **Primary Financial Driver** | High efficiency for variable workloads; costs approach zero when queries stop running. | Variable cost directly linked to query optimization. Poorly written queries scanning full tables increase costs. | Highly predictable budgeting. You pay a flat rate for the cluster regardless of whether it is active or completely idle. |

### Real-World Scenario
A multinational **E-commerce Platform** needs to run massive end-of-month financial reconciliation reports over terabytes of historical sales records. During the rest of the month, analytical queries are rare and irregular.

* **Decision:** **Snowflake** or **BigQuery**. Because compute and storage are decoupled or completely serverless, the company only pays for the intensive computing power during the brief end-of-month execution window. Choosing Amazon Redshift would force the company to pay for idle cluster hardware throughout the remaining 29 days of the month.

---

## 4. In-Memory Databases

In-Memory databases store data directly within system RAM rather than relying on traditional mechanical or solid-state disk drives. They are implemented to solve problems centered around ultra-low latency requirements (microseconds to milliseconds) or to handle temporary, fast-access transient data profiles.

### Available Options & Problem Profiles

* **Memcached:** A simple, high-performance, multithreaded key-value caching system designed for stripping away database load via basic string mappings.
* **Redis (Remote Dictionary Server):** A powerful in-memory data structure store supporting strings, hashes, lists, sets, and sorted sets with built-in persistence options and replication features.

### Selection Criteria: Caching vs. Advanced Data Processing

The architectural dividing line relies on whether you need a simple lookup cache or a smarter data processor:

* **Choose Memcached** if you only require a simple key-value store to cache flat object structures (e.g., mapping a specific `user_id` string directly to a static `user_profile` JSON blob) without needing backend logic or structural manipulation.
* **Choose Redis** when your requirements scale past basic string mapping into complex application logic. 

#### Dedicated Redis Use Cases:
* **Session State Management:** Storing temporary user login tokens and session expirations.
* **Real-time Leaderboards:** Utilizing Redis `Sorted Sets` to automatically compute live user rankings.
* **Atomic Counters:** Tracking global performance metrics, page views, or ad clicks.
* **Rate Limiting:** Managing API access thresholds via atomic increments and time-to-live (TTL) attributes.

### Real-World Scenario
A fast-growing **Gaming Platform** requires a live, global leaderboard for a multiplayer game, showing the top 100 players out of millions of active entries updated in real time.

* **Decision:** **Redis**. Relying on an RDBMS to run an aggregate `ORDER BY score DESC` query across millions of rows every second would collapse database performance. Memcached cannot sort complex collections natively. Redis handles this efficiently using its built-in `Sorted Sets` (ZSET), updating scores and extracting ranks with an O(log N) time complexity directly within memory.

---

## 5. Object Storage

Object Storage architectures manage files as flat, un-nested objects containing data, metadata, and a unique identifier. This pattern scales infinitely for unstructured assets like images, videos, system backups, and raw log files. It is fundamentally unsuited for low-latency transactions or traditional complex database queries.

### Available Options
* **Amazon S3 (Simple Storage Service):** The industry-standard cloud object storage platform within AWS.
* **Google Cloud Storage (GCS):** Google's unified object storage platform, tightly integrated with modern analytics pipelines.
* **Azure Blob Storage:** Microsoft's scalable object storage framework optimized for the Azure ecosystem.

### The Realities of Vendor Lock-In

Vendor lock-in becomes a critical risk when migrating away from a cloud provider becomes financially prohibitive, architecturally destructive, or operationally risky. This trap is driven by three primary variables:

#### 1. Massive Data Accumulation
Suppose an organization stores **100 TB** of asset data within an Amazon S3 bucket. Moving this data to Google Cloud Storage requires transferring the entire 100 TB over public networks or dedicated pipes. The absolute time cost and operational bandwidth overhead create immediate inertia against migrating.

#### 2. The Data Egress Cost Trap
Cloud providers typically utilize an asymmetrical pricing topology: **Data Ingress (moving data into the cloud) is completely free, while Data Egress (moving data out of the cloud) is heavily penalized.**
* *Example:* Extracting 100 TB of data out of an AWS environment to a competing cloud provider will generate thousands of dollars in pure network egress fees, effectively turning your own data into a financial hostage.

#### 3. Deep Ecosystem Integration
Lock-in deepens when object storage is tightly integrated with proprietary serverless compute and analytical pipelines. If your system relies on an architectural chain where an Amazon S3 upload triggers an AWS Lambda function, which runs code to update an Amazon DynamoDB table and pushes analytical rows into Amazon Redshift, migrating away from S3 means completely rewriting the surrounding computing architecture.

### Summary Checklist: When is Lock-In a Serious Concern?
* The total volume of stored physical data reaches a scale where network extraction fees are budget-prohibitive.
* The frequency of cross-cloud data transfer is high.
* The system utilizes proprietary cloud-native integrations rather than open, cloud-agnostic application layers.