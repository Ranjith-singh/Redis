# Redis: High-Performance In-Memory Data Store

Redis (Remote Dictionary Server) is an open-source, in-memory data structure store used as a database, cache, and message broker. It is known for its exceptional performance and versatility.

## Key Features
- **In-Memory Storage:** Data is stored in primary memory (RAM) for ultra-fast access ("hot" data).
- **High Performance:** Capable of handling writes up to 110,000/sec and reads up to 81,000/sec.
- **Persistence:** Supports persisting data to disk to ensure durability.
- **Atomicity:** All Redis operations are atomic, ensuring data integrity.
- **Rich Data Types:** Supports Strings, Lists, Sets, Sorted Sets, Hashes, HyperLogLogs, and more.

---

## Installation

### Option 1: Docker (Recommended for Windows)
Running Redis in a Docker container is the easiest way to get started on Windows without version compatibility issues.

1.  **Pull the Redis Image:**
    ```bash
    docker pull redis
    ```
2.  **Run Redis Container:**
    ```bash
    docker run --name my-redis -d -p 6379:6379 redis
    ```
    *   `--name my-redis`: Assigns a name to the container.
    *   `-d`: Runs the container in the background (detached mode).
    *   `-p 6379:6379`: Maps port 6379 on your host machine to port 6379 in the container.

3.  **Connect to Redis CLI:**
    ```bash
    docker exec -it my-redis redis-cli
    ```

### Option 2: Windows Subsystem for Linux (WSL2)
If you prefer a native-like Linux experience on Windows.

1.  **Install WSL:**
    Open PowerShell as Administrator and run:
    ```powershell
    wsl --install
    ```
    (Restart your computer if prompted).

2.  **Install Redis in Ubuntu (WSL):**
    Open your Ubuntu terminal and run:
    ```bash
    sudo apt-get update
    sudo apt-get install redis-server
    ```

3.  **Start the Server:**
    ```bash
    redis-server
    ```

### Option 3: Ubuntu/Debian (Direct)
If you are on a native Linux machine:
```bash
sudo apt install redis-server
redis-server
```

---

## Data Types and Commands

### 1. Strings
Strings are the most basic Redis value type. Access is efficient.

*   **Initialize/Update:** `SET [key] [value]`
    ```bash
    SET mykey "Hello"
    ```
*   **Read:** `GET [key]`
    ```bash
    GET mykey
    ```
*   **Substring:** `GETRANGE [key] [start] [end]` (Indices are inclusive)
    ```bash
    GETRANGE mykey 0 3
    ```
*   **Length:** `STRLEN [key]`
    ```bash
    STRLEN mykey
    ```
*   **Expiration:** Set a Time-To-Live (TTL) for a key.
    ```bash
    EXPIRE mykey 10  # Expires in 10 seconds
    # Or set during initialization:
    SETEx mykey 10 "Temporary"
    ```

### 2. Integers (Counters)
Redis handles strings that represent integers specifically for atomic increment operations.

*   **Increment:** `INCR [key]`
    ```bash
    SET counter 10
    INCR counter     # Becomes 11
    ```
*   **Increment By:** `INCRBY [key] [increment]`
    ```bash
    INCRBY counter 5 # Becomes 16
    ```

### 3. General Key Management
*   **List all Keys:** `KEYS [pattern]`
    ```bash
    KEYS *           # Returns all keys
    ```
*   **Delete all Keys:** `FLUSHALL` (Clears the entire database)

### 4. Lists
Linked lists of strings. Good for queues or stacks.

*   **Push (Left/Right):** `LPUSH` / `RPUSH`
    ```bash
    LPUSH tasks "task1" # Adds to the head (left)
    RPUSH tasks "task2" # Adds to the tail (right)
    ```
*   **Remove:** `LREM [key] [count] [value]`
    ```bash
    LREM tasks 1 "task1"
    ```
*   **Access by Index:** `LINDEX [key] [index]`
    ```bash
    LINDEX tasks 0
    ```
*   **Insert:** `LINSERT [key] [BEFORE|AFTER] [pivot] [value]`
*   **Range:** `LRANGE [key] [start] [stop]`
    ```bash
    LRANGE tasks 0 -1 # Get all elements
    ```
*   **Set at Index:** `LSET [key] [index] [value]`
*   **Push if Exists:** `LPUSHX` / `RPUSHX` (Only pushes if the list exists)
*   **Sort:** `SORT [key]`

### 5. Sets
Unordered collections of unique strings.

*   **Add:** `SADD [key] [member...]`
    ```bash
    SADD myset "a" "b" "c"
    ```
*   **Remove:** `SREM [key] [member...]`
*   **Get All Members:** `SMEMBERS [key]`
*   **Cardinality (Size):** `SCARD [key]`
*   **Check Membership:** `SISMEMBER [key] [member]`
*   **Set Operations:**
    *   **Difference:** `SDIFF [key1] [key2]` (key1 - key2)
    *   **Intersection:** `SINTER [key1] [key2]`
    *   **Union:** `SUNION [key1] [key2]`
    *   **Store Result:** `SDIFFSTORE` / `SINTERSTORE` / `SUNIONSTORE` usage: `...STORE destination key1 key2`

### 6. Sorted Sets
Similar to Sets, but every member is associated with a score, used for ordering.

*   **Add:** `ZADD [key] [score] [member]`
    ```bash
    ZADD leaderboard 100 "Player1"
    ```
*   **Range:** `ZRANGE [key] [start] [stop] [WITHSCORES]`
    ```bash
    ZRANGE leaderboard 0 -1 WITHSCORES
    ```
*   **Count:** `ZCARD [key]` (Total members) or `ZCOUNT [key] [min] [max]` (Count within score range)
*   **Reverse Range:** `ZREVRANGE [key] [start] [stop]`
*   **Remove:** `ZREM [key] [member]`

### 7. Hashes (HashMaps)
Maps between string fields and string values, perfect for representing objects like User Profiles.

*   **Set Field(s):** `HSET`
    ```bash
    HSET user:1001 name "Alice" email "alice@example.com" age "28"
    ```
*   **Get Field(s):** `HGET` / `HMGET`
    ```bash
    HGET user:1001 name          # Returns "Alice"
    HMGET user:1001 name email   # Returns "Alice" "alice@example.com"
    ```
*   **Get All Data:** `HGETALL`
    ```bash
    HGETALL user:1001            # Returns all field-value pairs
    ```
*   **Keys and Values:** `HKEYS` / `HVALS`
    ```bash
    HKEYS user:1001              # Returns "name", "email", "age"
    HVALS user:1001              # Returns "Alice", "alice@example.com", "28"
    ```
*   **Increment Field:** `HINCRBY` (Good for counters inside objects)
    ```bash
    HINCRBY user:1001 age 1      # Age becomes 29
    ```
*   **Set if Not Exists:** `HSETNX`
    ```bash
    HSETNX user:1001 country "USA" # Sets only if 'country' field doesn't exist
    ```
*   **Delete Field:** `HDEL`
    ```bash
    HDEL user:1001 age           # Deletes the 'age' field
    ```
*   **Length:** `HLEN`
    ```bash
    HLEN user:1001               # Returns number of fields
    ```

### 8. HyperLogLog
Probabilistic data structure used to verify cardinality of unique elements (e.g., unique IP address visits) with very small memory footprint.

*   **Add:** `PFADD [key] [element...]`
*   **Count:** `PFCOUNT [key]`
*   **Merge:** `PFMERGE [destkey] [sourcekey...]`

---

## Advanced Concepts

### Transactions
Redis transactions ensure that a block of commands runs sequentially and atomically. No other request is served in the middle.

*   **Commands:** `MULTI` (start), `EXEC` (execute), `DISCARD` (abort).
*   **Example (Bank Transfer):**
    ```bash
    MULTI
    DECR user:A:balance
    INCR user:B:balance
    EXEC
    ```

### Scripts (Lua)
You can write Lua scripts to execute complex logic on the server side efficiently. This saves bandwidth and ensures atomicity.

*   **Example:** Return a custom string.
    ```bash
    EVAL "return 'Hello from Lua'" 0
    ```
*   `redis.call()` is used to call Redis commands inside Lua.

### Pub/Sub (Publish/Subscribe)
A messaging system where publishers send messages to channels, and subscribers receive them. Use cases: Chat apps, live notifications.

1.  **Subscriber:** Listens to "news" channel.
    ```bash
    SUBSCRIBE news
    ```
2.  **Publisher:** Sends message to "news".
    ```bash
    PUBLISH news "New article released!"
    # Subscriber instantly receives this message
    ```

### Connection and Security
*   **Default Port:** 6379.
*   **Databases:** Redis has 16 logical databases (0-15). Default is 0.
    ```bash
    SELECT 1  # Switch to database 1
    ```
*   **Security:** You can set a password in `redis.conf`.
    ```bash
    AUTH mypassword123
    ```

### GeoSpatial
Store coordinates and query proximity. Perfect for "Nearby Drivers" or "Store Locator" features.

1.  **Add Locations:**
    ```bash
    GEOADD drivers 13.361389 38.115556 "DriverA"
    GEOADD drivers 15.087269 37.502669 "DriverB"
    ```
2.  **Find Distance:**
    ```bash
    GEODIST drivers DriverA DriverB km  # Returns distance in Kilometers
    ```
3.  **Find Nearby (Radius):**
    ```bash
    GEORADIUS drivers 15 37 100 km      # Find drivers within 100km of coordinates (15, 37)
    ```

### Benchmarking
Tool to test Redis performance on your machine.

*   **Basic Run:**
    ```bash
    redis-benchmark -q -n 100000
    ```
    *   `-q`: Quiet mode (only shows requests/sec).
    *   `-n`: Total requests.
