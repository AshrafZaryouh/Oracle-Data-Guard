# 🌐 Oracle Data Guard — Comprehensive Overview

![hq720](https://github.com/user-attachments/assets/f433ff73-e2d6-46a0-aa31-047f0a55b965)

---

## 🧭 1. Purpose & Concept

![sbydb054](https://github.com/user-attachments/assets/6d0100ee-c25e-47ce-997e-a0627840599e)


Oracle Data Guard ensures **high availability, data protection, and disaster recovery** for Oracle databases.
It maintains **transactionally consistent standby databases** that can quickly assume the primary role if needed, ensuring minimal downtime and data loss.

---

## 🏗️ 2. Core Components

<img width="1344" height="632" alt="oci-cloud-core-infrastructure-services (1)" src="https://github.com/user-attachments/assets/308cc55d-50ff-4ca7-952a-9975ab61fc2c" />

| Component                   | Description                                                                          |
| --------------------------- | ------------------------------------------------------------------------------------ |
| **Primary Database**        | Main operational system handling user transactions. Sends redo to standby databases. |
| **Standby Database**        | Copy of the primary database — can be physical or logical.                           |
| **Redo Transport Services** | Transmits redo data from primary to standby.                                         |
| **Log Apply Services**      | Applies redo data to keep standby synchronized.                                      |
| **Data Guard Broker**       | Automates and manages the entire configuration.                                      |
| **Role Transitions**        | Enables switchover, failover, and reinstatement between databases.                   |

---

## 🗂️ 3. Types of Standby Databases

<img width="1025" height="436" alt="dataguard-dr-db" src="https://github.com/user-attachments/assets/9d2b280a-1fd5-481d-8b8c-c5e0befbac32" />


| Type                    | Description                                                   | Apply Method              |
| ----------------------- | ------------------------------------------------------------- | ------------------------- |
| 🧱 **Physical Standby** | Exact, block-for-block replica of primary.                    | Redo Apply                |
| 📜 **Logical Standby**  | Contains identical data, but physical structure may vary.     | SQL Apply                 |
| 🔄 **Snapshot Standby** | Read-write enabled version of physical standby (for testing). | Redo Apply (after revert) |

---

## 🛡️ 4. Protection Modes

| Mode                        | Goal                             | Commit Behavior              | Data Loss        |
| --------------------------- | -------------------------------- | ---------------------------- | ---------------- |
| 🟩 **Maximum Protection**   | Zero data loss                   | Synchronous commit           | None             |
| 🟨 **Maximum Availability** | High protection with flexibility | Synchronous (fallback async) | Minimal          |
| 🟧 **Maximum Performance**  | Optimize throughput              | Asynchronous                 | Possible minimal |

---

## 🔗 5. Redo Transport Services

| Component                   | Function                                    |
| --------------------------- | ------------------------------------------- |
| **LGWR**                    | Writes redo to local logs.                  |
| **LNS / ARCH**              | Sends redo data to standby (SYNC/ASYNC).    |
| **RFS**                     | Receives redo data on standby.              |
| **SRL (Standby Redo Logs)** | Captures incoming redo for real-time apply. |

---

## ⚙️ 6. Log Apply Services

| Component                          | Function                                               |
| ---------------------------------- | ------------------------------------------------------ |
| **MRP (Managed Recovery Process)** | Applies redo data on physical standby.                 |
| **LSP (Logical Standby Process)**  | Converts redo into SQL statements for logical standby. |

---

## 🔁 7. Role Management

| Operation      | Description                                  | Trigger             |
| -------------- | -------------------------------------------- | ------------------- |
| **Switchover** | Planned role exchange (no data loss).        | Manual / Broker     |
| **Failover**   | Emergency role change (potential data loss). | Manual / Broker     |
| **Reinstate**  | Restores failed primary as standby.          | Automated by Broker |

---

## 🧩 8. Data Guard Broker

<img width="896" height="966" alt="dgbkr003" src="https://github.com/user-attachments/assets/3c302cbd-728b-4b6e-9368-ccb287ee8905" />


| Feature                 | Description                                                                         |
| ----------------------- | ----------------------------------------------------------------------------------- |
| **DGMGRL Utility**      | Command-line interface for administration.                                          |
| **Enterprise Manager**  | GUI-based management and monitoring.                                                |
| **Broker Config Files** | `DG_BROKER_CONFIG_FILE1` and `DG_BROKER_CONFIG_FILE2` store configuration metadata. |

---

## 🔍 9. Monitoring Views

| View                    | Purpose                                     |
| ----------------------- | ------------------------------------------- |
| `V$DATABASE`            | Displays database role & protection mode.   |
| `V$DATAGUARD_STATUS`    | Tracks Data Guard messages & errors.        |
| `V$ARCHIVE_DEST_STATUS` | Shows redo destination details.             |
| `V$MANAGED_STANDBY`     | Displays active Data Guard processes.       |
| `V$DATAGUARD_STATS`     | Shows lag, apply rate, and sync statistics. |

---

## ⚙️ 10. Key Configuration Parameters

| Parameter                   | Description                                             |
| --------------------------- | ------------------------------------------------------- |
| `LOG_ARCHIVE_CONFIG`        | Enables redo transport and defines destinations.        |
| `LOG_ARCHIVE_DEST_n`        | Specifies redo destinations.                            |
| `FAL_SERVER` / `FAL_CLIENT` | Handles archive gap resolution.                         |
| `STANDBY_FILE_MANAGEMENT`   | Automates file creation (`AUTO` recommended).           |
| `DB_UNIQUE_NAME`            | Uniquely identifies each database in the configuration. |

---

## 🧱 11. Schema-Level Behavior

<img width="900" height="424" alt="gg-config-modes" src="https://github.com/user-attachments/assets/13059537-8976-42e1-86a8-b66bc38506e0" />

| Aspect                  | Physical Standby                            | Logical Standby                                       |
| ----------------------- | ------------------------------------------- | ----------------------------------------------------- |
| **Schema Replication**  | Exact copy of all users, schemas, and data. | Logical synchronization of objects.                   |
| **DDL/DML Propagation** | All committed redo applied.                 | SQL reconstruction and execution.                     |
| **Unsupported Objects** | Fully supported.                            | Some types (e.g., sequences) may require workarounds. |

---

## 💻 12. Broker Command Examples

| Task          | Example Command                                                                              |
| ------------- | -------------------------------------------------------------------------------------------- |
| Create config | `CREATE CONFIGURATION 'DGCONF' AS PRIMARY DATABASE IS 'PRIM' CONNECT IDENTIFIER IS prim_dg;` |
| Add standby   | `ADD DATABASE 'STBY' AS CONNECT IDENTIFIER IS stby_dg MAINTAINED AS PHYSICAL;`               |
| Enable config | `ENABLE CONFIGURATION;`                                                                      |
| Switchover    | `SWITCHOVER TO 'STBY';`                                                                      |
| Failover      | `FAILOVER TO 'STBY';`                                                                        |
| Show status   | `SHOW CONFIGURATION;`                                                                        |

---

## ⚡ 13. Performance & Optimization

| Factor               | Recommendation                             |
| -------------------- | ------------------------------------------ |
| **Network Latency**  | Use compression or asynchronous transport. |
| **Redo Apply Lag**   | Enable real-time apply with SRLs.          |
| **I/O Optimization** | Separate SRLs and datafiles.               |
| **Monitoring**       | Use AWR and Data Guard Broker logs.        |

---

## 🌍 14. Common Use Cases

| Scenario                   | Description                                                  |
| -------------------------- | ------------------------------------------------------------ |
| 🆘 **Disaster Recovery**   | Automatic or manual failover in case of primary failure.     |
| 🔄 **Rolling Upgrade**     | Logical or transient standby enables zero-downtime upgrades. |
| 📊 **Reporting & Queries** | Logical or snapshot standby opened for analytics.            |

---

