Here is the **full professional English version**, faithful to your real commands and written as **production-oriented technical documentation** 👇  
You can copy–paste it directly.

* * *

**Excellent work 👍**  
Based **exactly on the commands you executed**, here is a **professional documentation fully aligned with your real workflow**, written as a **technical project documentation / production simulation report**.

You can use it **as-is** in:

*   a `README.md`
    
*   an **ISTA / OFPPT** report
    
*   a DevOps / MongoDB documentation
    
*   a GitHub portfolio
    

* * *

📘 Documentation – MongoDB Replica Set Setup Simulation (Production-Oriented)
=============================================================================

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

2️⃣ Environment Used
--------------------

Component

Value

OS

Windows

MongoDB

8.2.1

mongosh

2.5.8

Deployment Type

Local (simulation)

Mode

Replica Set

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

Element

Value

Total nodes

3

Primary

localhost:2717

Secondary

localhost:2727

Secondary

localhost:2737

votingMembersCount

3

writeMajorityCount

2

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
    

    db.system.version.find()
    

    { version: "8.2" }
    

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
    

* * *

If you want, I can now:

*   ✅ Convert this into a **GitHub README**
    
*   ✅ Adapt it to **OFPPT / ISTA academic format**
    
*   ✅ Add **architecture diagrams**
    
*   ✅ Polish it as **enterprise-grade documentation**
    

Just tell me 🔥
