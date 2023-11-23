# short url

http://tinyurl.com

### functional requirements
* generate (or optionally pick) a shorter unique alias for an original url
* redirect to an original url
* expiration

### non-functional requirements
* high availability (mainly for a redirection)
* low latency (mainly for a redirection)
* not predictable aliases

### extended requirements
* analytics
* api

### traffic estimates
* 10^9 writes per month
* 1000:1 read/write ratio
* w: 10^9 / (30 x 10^5) = 300 qps
* r: 300 x 10^3 = 300K qps

### storage estimates
* 5 years or 60 months
* 1KB per record (average)
* 10^9 x 60 x 1KB = 60TB

### bandwidth estimates
* w: 1KB x 300 qps = 300KB/s
* r: 1KB x 300K qps = 300MB/s
* ingress and egress are not big!

### memory estimates
* 80/20 rule (20% of urls generate 80% of traffic)
* 20%: 300 x 10^5 x 0.2 = 30 x 10^6 x 0.2 = 6 x 10^6 per day
* 6 x 10^6 x 1KB = 6 x 10^9 = 6GB

### estimates
* urls: 300 per second
* redirections: 300K per second
* incoming: 300KB/s
* outcoming: 300MB/s
* storage: 60TB
* cache: 6GB

### api
* create_url(api_key, url, alias=none, expires_at=none) -> string
* remove_url(api_key, alias) -> boolean
* http 302 redirect or http 404 not found

### database
* data: alias (hash pk), url, created, expires_at, user
* user: id, email, password_hash, api_key
* store billions of records and even more
* small record
* no relations between entities
* read-heavy
* nosql

### approach 1
* compute md5 or sha256 and then encode for displaying using base36 ([a-z,0-9]), base62 ([A-Z,a-z,0-9]) or base64, take first characters
* 6-character long key generates 64^6 = 68 x 10^9 unique keys (enough)
* 7-character long key generates 64^7 = 4 x 10^12 unique keys
* !!! hashes can have collisions by its nature
* !!! the same urls have the same hashes
* !!! url must be escaped
* write to master, read from replicas

### approach 2 (kgs)
* pre-generate keys
* 68 x 10^9 x 6 = 412GB
* write to master, read from replicas

### approach 3
* split key ranges among servers (68 servers 10^9 values each, or 680 servers 10^8 values each)
* write index to the database periodically
* jump on the next valid index after a crash

### partitioning
* hash-based partitioning (consistent hashing)

### cache
* lru evict policy

### load balancer
* between clients and application servers
* between application servers and database servers
* between application servers and cache servers
* round robin (server load is not taken into consideration)

### purge
* background cleanup
