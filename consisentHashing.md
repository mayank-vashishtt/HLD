# Modified Consistent Hashing and Data Migration

## Introduction
Consistent hashing is a technique used in distributed systems to distribute data across multiple servers or databases efficiently. However, in real-world scenarios, traditional consistent hashing can suffer from cascading failures, leading to inefficiencies. To address these issues, **modified consistent hashing** was introduced, which utilizes multiple hash functions to improve load balancing and fault tolerance.

---

## Cascading Failure
### What is Cascading Failure?
Cascading failure occurs when the failure of a single node in a distributed system leads to a chain reaction, causing multiple subsequent failures and degrading overall system performance.

### Causes of Cascading Failure
1. **Uneven Load Distribution:** If a heavily loaded node fails, its data must be redistributed, potentially overloading other nodes.
2. **High Data Movement:** Inconsistent load balancing may lead to massive data migration when nodes join or leave.
3. **Poor Failure Handling:** If a system does not have redundancy mechanisms, failures propagate easily.

### How Modified Consistent Hashing Mitigates Cascading Failure
1. **Multiple Hash Functions:** Distributing data across multiple locations reduces the impact of node failure.
2. **Virtual Nodes:** Instead of a single entry per node, multiple entries distribute load more evenly.
3. **Gradual Data Migration:** Using shadow writes (as explained later) ensures smooth transitions during failures.

---

## Modified Consistent Hashing
### How It Works
Instead of relying on a single hash function, **modified consistent hashing** uses multiple hash functions to determine the placement of data in a distributed system.

#### Steps:
1. **Hashing Nodes (Databases)**:
   - We hash each node (database) multiple times using different hash functions.
   - These hashes are placed on a **consistent hash ring**.

2. **Hashing Data**:
   - Each data element (e.g., user ID) is hashed using the same set of hash functions.
   - This results in multiple possible locations for storing the data.

3. **Assigning Data**:
   - The data is stored in the closest node (clockwise on the hash ring).
   - This allows better load balancing since each node gets multiple positions on the ring.

### Code Implementation (Python Example)
```python
import hashlib
import bisect

class ModifiedConsistentHashing:
    def __init__(self, num_replicas, nodes):
        self.num_replicas = num_replicas  # Number of virtual nodes per database
        self.ring = {}
        self.sorted_keys = []
        self.nodes = nodes
        self._create_ring()
    
    def _hash(self, key, replica):
        return int(hashlib.md5(f"{key}_{replica}".encode()).hexdigest(), 16)

    def _create_ring(self):
        for node in self.nodes:
            for replica in range(self.num_replicas):
                hash_val = self._hash(node, replica)
                self.ring[hash_val] = node
                bisect.insort(self.sorted_keys, hash_val)

    def get_node(self, key):
        hash_val = self._hash(key, 0)  # Use one hash function to locate the node
        index = bisect.bisect_right(self.sorted_keys, hash_val) % len(self.sorted_keys)
        return self.ring[self.sorted_keys[index]]

# Example usage
nodes = ["DB1", "DB2", "DB3"]
mch = ModifiedConsistentHashing(num_replicas=3, nodes=nodes)

print(mch.get_node("user123"))  # Output: Assigned database
```

### Time Complexity Analysis
- **Hashing a key**: `O(1)` (constant time hashing)
- **Insertion into sorted list**: `O(log N)` (where `N = x * y`, total virtual nodes)
- **Finding a node**: `O(log N)` (binary search on sorted keys)

### Explanation of `x * y`
- `x` = Number of hash functions (or virtual replicas per node)
- `y` = Number of databases (nodes)
- Total virtual nodes = `x * y`
- More virtual nodes → Better load distribution

### Limitations and Solutions
1. **More Virtual Nodes = More Memory Usage**
   - Solution: Optimize by using only a necessary number of replicas.

2. **Increased Computation for Multiple Hashes**
   - Solution: Use efficient hash functions and caching.

3. **Data Migration on Node Addition/Removal**
   - Solution: Shadow new writes (explained below).

---

## Principle of Data Migration
When adding or removing a node, we need to migrate data efficiently.

### Shadowing New Writes Strategy
1. **New Writes Redirected to D4**
   - A new node `D4` is added.
   - New writes go to `D4` but also shadowed to `D2` (existing node) for consistency.

2. **Warming Up D4**
   - At `timestamp 1`, new data is written to both `D4` and `D2`.
   - At the same time, old data from `D2` is copied to `D4`.

3. **Finalizing Migration**
   - At `timestamp 2`, `D4` is fully warmed up and starts serving requests.
   - `D2` is removed from the system.

### Why This Approach?
- **Ensures No Data Loss**
- **Minimizes Read/Write Disruptions**
- **Allows Smooth Transition Without Downtime**

---

## Conclusion
Modified consistent hashing improves load balancing by using multiple hash functions. However, it requires efficient data migration strategies like shadowing new writes to handle dynamic changes in distributed systems. Additionally, cascading failures can be mitigated through proper load balancing, redundancy, and controlled data migration strategies.

