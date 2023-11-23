# pastebin

http://pastebin.com

### functional requirements
* upload text data and generate (or optionally pick) a unique url
* expiration

### non-functional requrements:
* high reliability (data should not be lost)
* high availability
* low latency
* url should not be guessable

### extended requirements
* analytics
* api

### limits
* 1MB data

### traffic estimates
* 10^9 writes per month
* 5:1 read/write ratio
* w: 10^9 / (30 x 10^5) = 300 qps
* r: 300 qps x 5 = 1500 qps

### storage etimates
* 5 year or 60 months
* 10KB per record (average)
* 10^9 x 60 x 10KB = 600TB

### bandwidth estimates
* w: 10KB x 300 qps = 300KB/s
* r: 10KB x 1500 qps = 1.5MB/s
* ingress and egress are not big!

### memory estimates
* 80/20 rule (20% of urls generate 80% of traffic)
* 20%: 1.5MB/s x 10^5 x 0.2 = 300GB

### estimates
* writes: 300 per second
* reads: 1500 per second
* incoming: 300KB/s
* outcoming: 1.5MB/s
* storage: 600TB
* cache: 300GB

### api
* create(api_key, text, alias=none, expires_t=none) -> string
* remove(api_key, alias) -> boolean
* get(alias) -> string
* http 200 ok or http 404 not found

### database
* data: alias (hash pk), text, created, expires_at, user
* user: id, email, password_hash, api_key
* store billions of records and even more
* medium record (separate it from keys and other metadata, use s3?)
* no relations between entities
* read-heavy
* nosql

### approach (kgs)
* pre-generate keys
* 6-character long key generates 64^6 = 68 x 10^9 unique keys (enough)
* 7-character long key generates 64^7 = 4 x 10^12 unique keys
* 68 x 10^9 x 6 = 412GB
* write to master, read from replicas

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
