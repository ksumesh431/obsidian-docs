## **1. Topology Design**

### **1.1 Leader for Writes, Followers for Reads**

**Description:** In a Leader-Follower (Master-Slave) topology, all write operations are directed to the leader node, while read operations are distributed among follower nodes. This design improves read scalability and balances the load effectively.

**Verification Steps:**

1. **Identify Leader and Follower Nodes:**
    
    - **Command:**
        
        
        `redis-cli -h <node_host> -p <node_port> INFO replication`
        
    - **Explanation:**
        - Run this command on each Redis node.
        - The output will indicate the role of the node (`role:master` or `role:slave`).
    - **Example:**
        

        
        `redis-cli -h amqp-publisher3.euhpc.arm.com -p 6379 INFO replication`
        
    - **Expected Output:**
        

        
        `role:master connected_slaves:1 slave0:ip=amqp-publisher4.euhpc.arm.com,port=6379,state=online,offset=123456,lag=0`
        
        or
        

        
        `role:slave master_host:amqp-publisher3.euhpc.arm.com master_port:6379 master_link_status:up`
        
2. **Check Application Read/Write Routing:**
    
    - **Review Application Configuration:**
        - Examine how your applications are configured to interact with Redis.
        - Ensure that:
            - **Write Operations:** Target the leader node.
            - **Read Operations:** Distribute across follower nodes.
    - **Application Code Review:**
        - Look into the codebase or configuration files to confirm the routing logic.
    - **Example in Code (Pseudo-code):**
        

        
        `# For writes redis_write_client = Redis(host='leader_host', port=6379)  # For reads redis_read_clients = [     Redis(host='follower1_host', port=6379),     Redis(host='follower2_host', port=6379), ]`
        
    - **Load Balancing Verification:**
        - If using a proxy or load balancer, check its configuration to ensure proper distribution of read requests.
3. **Monitor Traffic Patterns:**
    
    - **Use Redis Monitoring Tools:**
        - Tools like `redis-cli monitor`, Redis Slow Log, or external monitoring solutions can help observe real-time operations.
    - **Commands:**
        

        
        `redis-cli -h <leader_host> -p 6379 monitor`
        
        - Observe that write commands (`SET`, `DEL`, etc.) are hitting the leader.
        

        
        `redis-cli -h <follower_host> -p 6379 monitor`
        
        - Observe that read commands (`GET`, `MGET`, etc.) are hitting the followers.

**Documentation Notes:**

- **Current Status:** Document whether write and read operations are correctly routed.
- **Recommendations:**
    - If reads are hitting the leader, adjust application configurations.
    - Consider implementing client-side logic or use middleware/proxies to distribute read loads.

---

### **1.2 Minimize Follower Lag**

**Description:** Replication lag occurs when followers are not up-to-date with the leader's data. Minimizing this lag ensures data consistency and reduces the risk of serving stale data.

**Verification Steps:**

1. **Check Replication Lag on Followers:**
    
    - **Command:**
        

        
        `redis-cli -h <follower_host> -p 6379 INFO replication`
        
    - **Explanation:**
        - Look for the `master_last_io_seconds_ago` and `lag` metrics.
    - **Expected Output:**
        

        
        `master_last_io_seconds_ago:0 master_sync_in_progress:0`
        
    - **Interpretation:**
        - `master_last_io_seconds_ago` should be as low as possible (ideally 0).
        - A high value indicates significant lag.
2. **Monitor Continuously:**
    
    - **Use Monitoring Tools:**
        - Set up continuous monitoring using tools like **Prometheus** or **Datadog** to track replication lag over time.
    - **Set Alerts:**
        - Configure alerts to notify when lag exceeds acceptable thresholds.
3. **Evaluate Network Performance:**
    
    - **Check Network Latency:**
        
        - Use tools like `ping` and `traceroute` between leader and follower nodes.
        

        
        `ping <follower_host> traceroute <follower_host>`
        
    - **Interpretation:**
        - High latency or packet loss can contribute to replication lag.
4. **Assess Resource Utilization:**
    
    - **Monitor CPU and Memory:**
        - High CPU or memory usage can slow down replication.
        - Use `top`, `htop`, or monitoring tools to assess.
    - **Disk I/O Performance:**
        - Ensure disks are not bottlenecks; check with `iostat`.
5. **Review Redis Configuration Parameters:**
    
    - **Key Parameters to Check:**
        - `repl-backlog-size`: Adequate backlog size helps in handling temporary disconnections without full resyncs.
        - `repl-diskless-sync`: Can improve synchronization speed.
    - **Command to View Config:**
        

        
        `redis-cli -h <follower_host> -p 6379 CONFIG GET repl-*`
        
    - **Example Output:**
        
        makefile
        
        Copy code
        
        `repl-backlog-size:1048576 repl-diskless-sync:no`
        
    - **Recommendations:**
        - Increase `repl-backlog-size` if frequent partial resyncs fail.
        - Enable `repl-diskless-sync` for faster initial synchronization, especially over high-speed networks.

**Documentation Notes:**

- **Current Status:** Note current replication lag metrics and configurations.
- **Recommendations:**
    - Tweak configurations as needed.
    - Improve network conditions if lag is network-related.
    - Scale resources if hardware limitations cause lag.

---

### **1.3 Use at Least 3 Nodes**

**Description:** Having at least one leader and two followers enhances redundancy and ensures availability even if one follower fails. It also allows better load distribution for read operations.

**Verification Steps:**

1. **Enumerate All Redis Nodes:**
    
    - **Compile a List:**
        - Document all current Redis nodes in your infrastructure.
    - **Command:**
        

        
        `# On leader redis-cli -h <leader_host> -p 6379 INFO replication`
        
    - **Expected Output:**

        
        `connected_slaves:2 slave0:ip=<follower1_ip>,port=6379,state=online,offset=123456,lag=0 slave1:ip=<follower2_ip>,port=6379,state=online,offset=123457,lag=0`
        
    - **Interpretation:**
        - `connected_slaves` should be 2 or more.
2. **Check Redundancy and Load Balancing:**
    
    - **Review Read Distribution:**
        - Ensure that read operations are appropriately balanced among followers.
    - **Simulate Node Failure:**
        - Temporarily stop one follower and observe whether the system continues to operate normally.
        - **Commands:**
            
            
            `sudo systemctl stop redis`
            
        - **After Testing:**
            
            
            `sudo systemctl start redis`
            
3. **Evaluate Failover Scenarios:**
    
    - **Leader Failure:**
        - Check if your setup can handle leader failure gracefully (this ties into Redis Sentinel configuration, discussed later).
    - **Follower Failure:**
        - Ensure remaining followers can handle the increased read load.

**Documentation Notes:**

- **Current Status:** Document the number of leader and follower nodes present.
- **Recommendations:**
    - If only one follower exists, consider adding additional followers for improved redundancy.
    - Update load balancers or application configurations to utilize additional followers.

---

## **2. Replication Configuration**

### **2.1 Asynchronous Replication**

**Description:** Asynchronous replication allows followers to continue operating even if the leader is down, enhancing performance and availability. However, there's a risk of data loss if the leader fails before replication completes.

**Verification Steps:**

1. **Check Replication Mode:**
    
    - **Understanding Redis Default:**
        - Redis uses asynchronous replication by default.
    - **Confirm No Synchronous Settings:**
        - **Command:**
            
            
            `redis-cli -h <leader_host> -p 6379 CONFIG GET replica-*`
            
        - **Parameters to Check:**
            - Ensure parameters like `min-slaves-to-write` and `min-slaves-max-lag` are not set in a way enforcing synchronous behavior.
        - **Example Output:**
            
            `replica-read-only:yes replica-serve-stale-data:yes`
            
        - **Interpretation:**
            - The absence of `min-slaves-to-write` and `min-slaves-max-lag` indicates purely asynchronous replication.
2. **Test Behavior During Leader Downtime:**
    
    - **Simulate Leader Failure:**
        - Stop the leader and observe follower behavior.
        - **Commands:**
            
            `sudo systemctl stop redis`
            
        - **Observe:**
            - Followers should continue serving read requests.
            - Write operations will fail unless failover mechanisms are in place.
3. **Review Application Handling:**
    
    - **Application Resilience:**
        - Check if your applications handle the possibility of slightly stale data due to asynchronous replication.
    - **Error Handling:**
        - Ensure appropriate error handling for write operations during leader downtime.

**Documentation Notes:**

- **Current Status:** Confirm that replication is asynchronous and functioning as expected.
- **Recommendations:**
    - If synchronous replication is required for critical data, consider configuring `WAIT` command usage selectively in your application code.

---

### **2.2 Diskless Replication**

**Description:** Diskless replication allows sending the RDB dump directly over the network to followers during synchronization, reducing disk I/O and speeding up the process, especially beneficial in high-load environments.

**Verification Steps:**

1. **Check Diskless Replication Configuration:**
    
    - **Command:**
        
        
        `redis-cli -h <leader_host> -p 6379 CONFIG GET repl-diskless-sync`
        
    - **Expected Output:**
        
        
        `repl-diskless-sync:yes`
        
    - **Additional Parameters:**
        
        - `repl-diskless-sync-delay`: Time Redis waits before starting diskless replication to allow multiple followers to synchronize simultaneously.
        
        
        `redis-cli -h <leader_host> -p 6379 CONFIG GET repl-diskless-sync-delay`
        
    - **Interpretation:**
        - If `repl-diskless-sync` is `no`, diskless replication is not enabled.
2. **Evaluate Performance Benefits:**
    
    - **Monitor During Sync:**
        - Observe CPU and network usage during follower synchronization.
    - **Compare with Disk-based Sync:**
        - If possible, compare metrics with disk-based synchronization to assess improvements.
3. **Assess Network Reliability:**
    
    - **Requirement:**
        - Diskless replication is more sensitive to network issues. Ensure a stable and reliable network between leader and followers.
4. **Check for Potential Issues:**
    
    - **Large Data Sets:**
        - For very large datasets, diskless replication may consume significant network bandwidth.
    - **Fallback Mechanisms:**
        - Ensure configurations allow for fallback to disk-based replication if diskless replication fails.

**Documentation Notes:**

- **Current Status:** Note whether diskless replication is enabled.
- **Recommendations:**
    - If not enabled and suitable for your environment, consider enabling it by setting:
        
        
        `CONFIG SET repl-diskless-sync yes`
        
    - Adjust `repl-diskless-sync-delay` based on synchronization patterns.

---

### **2.3 Minimize Full Resynchronizations**

**Description:** Full resynchronizations are resource-intensive and time-consuming. Configuring replication backlog effectively helps enable partial resynchronizations, reducing overhead during reconnections.

**Verification Steps:**

1. **Check Replication Backlog Configuration:**
    
    - **Commands:**
        
        
        `redis-cli -h <leader_host> -p 6379 CONFIG GET repl-backlog-size redis-cli -h <leader_host> -p 6379 CONFIG GET repl-backlog-ttl`
        
    - **Expected Output:**
        
        
        `repl-backlog-size:1048576  # 1MB by default repl-backlog-ttl:3600      # 1 hour by default`
        
    - **Interpretation:**
        - **`repl-backlog-size`** should be large enough to accommodate the volume of write operations between reconnections.
        - **`repl-backlog-ttl`** determines how long the backlog is maintained after the last follower disconnects.
2. **Monitor Partial Resynchronizations:**
    
    - **Check Redis Logs:**
        - Look for logs indicating whether partial or full resynchronizations occur during follower reconnections.
        - **Example Log Entries:**
            
            
            `Partial resynchronization request accepted. Full resync from master: a1b2c3d4e5f6...`
            
    - **Analysis:**
        - Frequent full resyncs indicate the backlog size or TTL may be insufficient.
3. **Evaluate Network Stability:**
    
    - **Frequent Disconnections:**
        - If followers disconnect often, increasing backlog size and TTL can help utilize partial resyncs more effectively.
4. **Adjust Backlog Settings:**
    
    - **Recommendations:**
        - Increase `repl-backlog-size` if:
            - Your write volume is high.
            - Followers experience frequent short-term disconnections.
        - **Command to Adjust:**
            
            
            `redis-cli -h <leader_host> -p 6379 CONFIG SET repl-backlog-size 10485760  # 10MB redis-cli -h <leader_host> -p 6379 CONFIG SET repl-backlog-ttl 7200       # 2 hours`
            

**Documentation Notes:**

- **Current Status:** Document current backlog configurations and resync patterns.
- **Recommendations:**
    - Adjust backlog size and TTL based on observed replication behaviors and workloads.

---

### **2.4 Configure Replication Backlog**

**Description:** Properly configuring the replication backlog ensures smooth replication by buffering data changes, allowing followers to catch up without full synchronization after brief disconnections.

**Verification Steps:**

1. **Review Current Backlog Settings:**
    
    - **Commands:**
        
        
        `redis-cli -h <leader_host> -p 6379 CONFIG GET repl-backlog-size redis-cli -h <leader_host> -p 6379 INFO replication`
        
    - **Check Current Usage:**
        - The `INFO replication` output includes `repl_backlog_active` and `repl_backlog_size`.
    - **Interpretation:**
        - Ensure the backlog is active and appropriately sized.
2. **Analyze Write Volume:**
    
    - **Estimate Write Traffic:**
        - Determine the average amount of write data generated over typical disconnection periods