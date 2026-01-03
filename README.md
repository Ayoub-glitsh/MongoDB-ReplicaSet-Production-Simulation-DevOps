
📘 Documentation – MongoDB Replica Set Setup Simulation (Production-Oriented)
=============================================================================
<div style="display: flex; flex-wrap: wrap; justify-content: center; gap: 10px;">

<!-- Ligne 1 -->
<img src="img/replicaSet_01.png" alt="Replica Set 1" width="200" height="150" />
<img src="img/replicaSet_02.png" alt="Mongo Shell 1" width="200" height="150" />
<img src="img/replicaSet_03.png" alt="Election 1" width="200" height="150" />
<img src="img/replicaSet_04.png" alt="Replication 1" width="200" height="150" />

<!-- Ligne 2 -->
<img src="img/replicaSet_05.png" alt="Error 1" width="200" height="150" />
<img src="img/replicaSet_06.png" alt="Replica Set 2" width="200" height="150" />
<img src="img/replicaSet_07.png" alt="Mongo Shell 2" width="200" height="150" />
<img src="img/replicatSet_08.png" alt="Election 2" width="200" height="150" />

<!-- Ligne 3 -->
<img src="img/replicaSet_09.png" alt="Replication 2" width="200" height="150" />
<img src="img/replicaSet_10.png" alt="Error 2" width="200" height="150" />
<img src="img/replicaSet_11.png" alt="Replica Set 3" width="200" height="150" />
<img src="img/replicaSet_12.png" alt="Mongo Shell 3" width="200" height="150" />

</div>








Replica Set Name: `myReplicaSet`
--------------------------------

* * *

1️⃣ Simulation Objective
------------------------

The objective of this simulation is to **set up and operate a MongoDB Replica Set cluster** in a production-like environment in order to:

*   Understand the roles of **Primary** and **Secondary** nodes
*   Observe **automatic elections**  
*   Test **data replication**
*   Verify **write restrictions**
*   Diagnose **real-world production errors**
    

* * *


## 2️⃣ Environment Used

| Element             | Value               |
| ------------------- | ----------------- |
| OS                  | Windows           |
| MongoDB             | 8.2.1             |
| mongosh             | 2.5.8             |
| Deployment Type     | Local (simulation)|
| Mode                | Replica Set       |




* * *

3️⃣ Connection to the Initial Primary Node
------------------------------------------

Connection to MongoDB server on port **2717**:

    mongosh --port 2717
    

Result:

*   Successful connection
*   Node is **Primary**
*   Active Replica Set: `myReplicaSet`
    

* * *

4️⃣ Initial Replica Set Status Check
------------------------------------

Executed command:

    rs.status()
    
### 🔍 Observation

*   `votingMembersCount: 1`
*   `stateStr: PRIMARY`
*   Only one active node
*   No fault tolerance yet
    
👉 The Replica Set is operational but **not redundant**

* * *

5️⃣ Adding Secondary Nodes
--------------------------

### ➕ Add first Secondary

    rs.add("localhost:2727")
    

### ➕ Add second Secondary

    rs.add("localhost:2737")
    

* * *

6️⃣ Cluster Status After Adding Nodes
-------------------------------------

    rs.status()
    

### 🔍 Result

| Element            | Value          |
| ------------------ | -------------- |
| Total nodes        | 3              |
| Primary            | localhost:2717 |
| Secondary          | localhost:2727 |
| Secondary          | localhost:2737 |
| votingMembersCount | 3              |
| writeMajorityCount | 2              |


✅ The cluster is now **highly available**

* * *

7️⃣ Shell Behavior Test (`rs.status` vs `rs.status()`)
------------------------------------------------------

Incorrect command:

    rs.status
    

Result:

*   Returns a **function reference**
*   No execution
    

Correct command:

    rs.status()
    

👉 This highlights the difference between:

*   **Function reference**
*   **Function execution**
    

* * *

8️⃣ Restart and Temporary Connection Loss
-----------------------------------------

Reconnection attempt:

    mongosh --port 2717
    

Result:

    MongoNetworkError: connect ECONNREFUSED
    

### 🔍 Interpretation

*   The node on port 2717 was **down**
*   The Replica Set triggered an **automatic election**
    

* * *

9️⃣ New Election Observation
----------------------------

Connection to another node:

    mongosh --port 2727
    rs.status()
    

### 🔍 Result

*   `localhost:2727` becomes **PRIMARY**
*   `localhost:2717` becomes **SECONDARY**
*   `term` incremented (moved to `term: 2`)
    

✅ **Fault tolerance confirmed**

* * *

🔟 Write Attempt on a Secondary (Expected Failure)
--------------------------------------------------

Connection to a Secondary:

    mongosh --port 2717
    

Insert attempt:

    db.users.insert({ "name": "my name is Ayoub" })
    

Result:

    MongoBulkWriteError[NotWritablePrimary]: not primary
    

### 🧠 Explanation

*   **Write operations are forbidden** on Secondary nodes
*   Only the **Primary** accepts writes
    

✅ Normal production behavior

* * *

1️⃣1️⃣ Data Insertion on the Primary
------------------------------------

Connection to the Primary:

    mongosh --port 2727
    

Successful insert:

    db.random.insertOne({ "name": "My name is Ayoub" })
    

Result:

    acknowledged: true
    

* * *

1️⃣2️⃣ Replication Verification
-------------------------------

Connection to a Secondary:

    mongosh --port 2717
    

    use Ayoub
    show collections
    

Result:

*   The `random` collection is present
    
*   Data has been **automatically replicated**
    

✅ Replication is functional

* * *

1️⃣3️⃣ Encountered Issue: BSON / UTF-8 Error
--------------------------------------------

Observed error:

    BSONError: Invalid UTF-8 string in BSON document
    

### 🔍 Identified Cause

*   MongoDB version **8.2**
*   Feature Compatibility Version:
    
```bash
    db.system.version.find()
    { version: "8.2" }
```   

👉 Version is too recent → instability with some commands (`rs.status()`)

* * *

1️⃣4️⃣ Security Warnings (Non-Production Mode)
----------------------------------------------

At startup:

    Access control is not enabled
    Server is bound to localhost
    

### 🔐 Interpretation

*   No authentication enabled
*   Access restricted to `localhost`
*   Acceptable for **simulation**
*   ❌ Not recommended for production
    

* * *

1️⃣5️⃣ Results and Skills Acquired
----------------------------------

### ✅ Validated Skills

*   Replica Set setup
*   Dynamic node addition
*   `rs.status()` analysis
*   Election mechanism understanding
*   Handling `NotWritablePrimary` errors
*   Automatic data replication
*   BSON / FCV issue diagnostics
    

* * *

1️⃣6️⃣ Conclusion
-----------------

This simulation successfully reproduced a **production-like MongoDB environment**, demonstrating:

*   Real Replica Set behavior
*   High availability
*   Write limitations
*   Automatic elections
*   Version-related errors
    
It provides a **solid foundation** for backend, DevOps, and cloud projects.

* * *

📌 Future Improvements
----------------------

*   Enable authentication (`--auth`)
*   Add TLS/SSL
*   Downgrade FCV to 7.0
*   Dockerize the cluster
*   Add monitoring (Prometheus / MongoDB Compass)
    




***


# MongoDB Replica Set Production Architecture

  

## 1️⃣ Production Architecture Overview

  

```mermaid
graph TB
    subgraph "Application Layer"
        App[Client Application]
    end
    subgraph "MongoDB Replica Set: myReplicaSet"
        P[Primary Node
localhost:2717]
        S1[Secondary Node 1
localhost:2727]
        S2[Secondary Node 2
localhost:2737]
    end
    subgraph "Data Storage"
        DP[(Primary Data
Oplog)]
        DS1[(Secondary Data 1
Replicated)]
        DS2[(Secondary Data 2
Replicated)]
    end
    App -- "Write Operations
Read/Write" --> P
    App -- "Read Operations
(Optional)" --> S1
    App -- "Read Operations
(Optional)" --> S2
    P -- "Heartbeat & Replication" --> S1
    P -- "Heartbeat & Replication" --> S2
    S1 -- "Heartbeat" --> S2
    P --- DP
    S1 --- DS1
    S2 --- DS2
    classDef primary fill:#e1f5fe,stroke:#0288d1,stroke-width:2px
    classDef secondary fill:#f3e5f5,stroke:#7b1fa2
    classDef storage fill:#e8f5e8,stroke:#2e7d32
    class P,DP primary
    class S1,DS1,S2,DS2 secondary
    class DP,DS1,DS2 storage
```

  

### 🛠️ Technical Explanation

This production architecture illustrates a **MongoDB Replica Set** with **three nodes** deployed on different ports on `localhost` (simulating separate servers).
---
- **🟢 Primary Node** (`localhost:2717`):  
  - Handles **all write operations**  
  - Coordinates **replication** to both Secondary nodes  
---
- **🔵 Secondary Nodes** (`localhost:2727` & `localhost:2737`):  
  - Serve **read operations** to distribute query load  
  - Receive data replicated from the Primary  
---
- **💓 Heartbeats & Cluster Health**:  
  - All nodes maintain **continuous communication**  
  - Enable **automatic failover** if the Primary goes down  
---
> ✅ This setup simulates a **production-ready MongoDB cluster**, providing **high availability**, **fault tolerance**, and **read scalability**.



  

## 2️⃣ Write Operation Flow

  

```mermaid

sequenceDiagram

    participant C as Client/Application

    participant P as Primary (2717)

    participant S1 as Secondary 1 (2727)

    participant S2 as Secondary 2 (2737)

    participant O as Oplog

    Note over P,S2: Initial State: All nodes synchronized

    C->>P: 1. Insert/Update/Delete Request

    P->>O: 2. Write to Oplog (capped collection)

    P->>C: 3. Acknowledge Write (immediately)

    par Replication to Secondaries

        P->>S1: 4. Oplog Entry Replication

        S1->>S1: 5. Apply Oplog Entry

        S1->>P: 6. Replication Acknowledgement

        and

        P->>S2: 4. Oplog Entry Replication

        S2->>S2: 5. Apply Oplog Entry

        S2->>P: 6. Replication Acknowledgement

    end

    Note over P,S2: Eventual Consistency: Secondaries catch up

```

  
### 📝 Technical Explanation – Write Flow

Write operations in a **MongoDB Replica Set** follow a **specific flow** to ensure **data durability** and **performance**:
---
1. **🧑‍💻 Client → Primary**  
   - The client sends **write operations exclusively** to the **Primary node**  
---
2. **🗂️ Primary Oplog Logging**  
   - Primary writes the operation to its **Oplog** (operations log)  
   - The Oplog is a **capped collection** that tracks all data changes  
---
3. **✅ Primary Acknowledgment**  
   - Primary immediately **acknowledges the write** to the client  
   - Default **write concern** ensures confirmation without waiting for secondaries  
---
4. **🔄 Replication to Secondaries**  
   - Primary **asynchronously replicates** Oplog entries to all Secondary nodes  
---
5. **📥 Secondary Application**  
   - Each Secondary **applies operations in the same order** as the Primary  
---
6. **📤 Replication Acknowledgment**  
   - Secondaries **acknowledge replication completion** back to the Primary  

> ⚡ This flow ensures **durable writes** while maintaining **high performance** through **asynchronous replication**.


  

## 3️⃣ Automatic Failover & Election Process

  

```mermaid

stateDiagram-v2

    [*] --> NormalOperation: Replica Set Initialized

    state NormalOperation {

        P[Primary Node
2717] --> S1[Secondary Node 1
2727]: Heartbeats

        P --> S2[Secondary Node 2
2737]: Heartbeats

        S1 --> S2: Heartbeats

    }

    NormalOperation --> PrimaryFailure: Primary Unreachable

    state PrimaryFailure {

        F[Primary Failed
No Heartbeat Response]

        S1 --> S2: Election Initiation

        S2 --> S1: Vote Exchange

    }

    PrimaryFailure --> ElectionInProgress: Majority Detects Failure

    state ElectionInProgress {

        S1 --> S1: Campaign for Primary

        S2 --> S2: Campaign for Primary

        S1 --> S2: Request Votes

        S2 --> S1: Grant Votes

    }

    ElectionInProgress --> NewPrimary: Majority Elects New Primary

    state NewPrimary {

        NP[New Primary
2727 Elected] --> S2[Secondary
2737]: Replication Resumes

        NP --> OF[Old Primary
2717]: Marked as Secondary
(When Recovered)

    }

    NewPrimary --> [*]: Stable State Achieved

```

  

### 🛠️ Technical Explanation – Automatic Failover
---
MongoDB implements **automatic failover** through the **Raft consensus algorithm**, ensuring high availability:

1. **💓 Heartbeat Monitoring**  
   - All nodes exchange **heartbeats every 2 seconds** to monitor cluster health  
---
2. **⚠️ Failure Detection**  
   - If **Secondaries** don't receive a heartbeat from the **Primary** within **10 seconds**, they initiate an **election**  
---
3. **🏁 Election Process**  
   - **Eligible Secondaries** (priority > 0, not hidden, up-to-date oplog) campaign to become **Primary**  
   - Nodes **vote** based on election criteria:  
     - Priority  
     - Data freshness  
     - Network connectivity  
   - Candidate requires **majority vote** (`n/2 + 1`) to be elected  
---
4. **🌟 New Primary**  
   - The **elected node transitions to Primary**  
   - Resumes **replication** to remaining Secondaries  
---
5. **🔄 Old Primary Recovery**  
   - When a **failed node recovers**, it rejoins as a **Secondary**  
   - Syncs **missing data** to catch up with the current Primary  

> ⚡ Automatic failover ensures **high availability**, **data consistency**, and **minimal downtime** in a MongoDB Replica Set.


  

## 4️⃣ Data Replication Synchronization

  

```mermaid

flowchart TD

    subgraph "Primary Node (2717)"

        direction LR

        P1[Application Data
Collections]

        P2[Oplog
Capped Collection]

        P1 -- "All Write Operations" --> P2

    end

    subgraph "Secondary Node 1 (2727)"

        direction LR

        S1_1[Sync Source
Primary's Oplog]

        S1_2[Data Apply
Replay Operations]

        S1_3[Local Data
Replicated Copy]

        S1_1 --> S1_2 --> S1_3

    end

    subgraph "Secondary Node 2 (2737)"

        direction LR

        S2_1[Sync Source
Primary's Oplog]

        S2_2[Data Apply
Replay Operations]

        S2_3[Local Data
Replicated Copy]

        S2_1 --> S2_2 --> S2_3

    end

    P2 -- "1. Oplog Entries Streamed
(tailable cursor)" --> S1_1

    P2 -- "1. Oplog Entries Streamed
(tailable cursor)" --> S2_1

    S1_2 -- "2. Apply in Original Order
(idempotent operations)" --> S1_3

    S2_2 -- "2. Apply in Original Order
(idempotent operations)" --> S2_3

    S1_1 -- "3. Heartbeat & Status Report" --> P2

    S2_1 -- "3. Heartbeat & Status Report" --> P2

```

  

### 🔄 Technical Explanation – Data Replication in MongoDB

---
MongoDB uses a **pull-based replication model** via the **Oplog** to ensure data consistency across Replica Set nodes.
---

#### 🗂️ Oplog Structure
- A **capped collection** storing **idempotent operations** (`insert`, `update`, `delete`) with **timestamps**  
- Serves as the source for replication to Secondaries  

---

#### 🔁 Replication Process
1. **Secondaries** maintain a **tailable cursor** on the Primary's Oplog  
2. **New operations** are streamed to Secondaries **in real-time**  
3. Each Secondary applies operations **in the same order as the Primary**  

---

#### ⚡ Initial Sync
- New nodes perform:  
  - **Full data copy**  
  - **Oplog application** to catch up with Primary  

---

#### ✅ Consistency Guarantees
- Controlled by **write concern options**:  
  - `w: 1` → Acknowledged by Primary  
  - `w: majority` → Acknowledged by most nodes  
  - `w: all` → Acknowledged by all nodes  

---

#### 📊 Lag Monitoring
- `rs.status()` / `replSetGetStatus` shows **replication lag**  
- Optimal production lag: **< 50ms**  

---

### 🏗️ Production Best Practices (3-node Replica Set)
- **Write Concerns**: Use `w: "majority"` for critical data to ensure durability  
- **Read Preferences**:  
  - `primary` → Strong consistency  
  - `secondaryPreferred` → Read scaling  
- **Connection String**:  
```text
mongodb://localhost:2717,localhost:2727,localhost:2737/?replicaSet=myReplicaSet
