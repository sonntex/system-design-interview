# instagram

### functional requirements
* upload, download and view photos
* search photos
* follow, unfollow
* news feed

### non-functional requirements
* high reliability (data should not be lost)
* high availability
* low latency (200ms for news feed generation)

### estimates
* 1 billion of users overall and 1 million of daily active users
* 2 million photos every day
* 200KB per photo
* 100 followers

### storage estimates
* 2 million x 200KB = 400GB per day
* 400GB * 365 * 10 = 1500TB per 10 years

### database schema
* users      :: id (pk), bio, created - 100B per record, 1 billion * 100B = 100GB for all users
* photos     :: id (pk), user (fk), path, metadata, created - 300B per record, 2 million * 300B = 500MB per day, 2TB per 10 years
* followings :: follower (pk), followee (fk) - 8B per record, 1 billion * 100 * 8B = 1TB
* use rdbms for entities
* use s3 for photos

### approach 1
* partitioning based on user id
* !!! non uniform distribution for photos

### approach 2
* partitioning based on photo id
* kgs
* resharding

### ranking and news feed generation
* create a table for the latest 100 feeds for each user
* fill it on any actions
* pull, push and hybrid mode (pull for million feeds and push for hundreds)

### cache
* lru evict policy
* cdn
