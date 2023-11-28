# dropbox

### functional requirements
* upload, donwload and view files
* share with others
* edit files online
* edit files offline
* support for large files
* strong consistency
* snapshots

### non-functional requirements
* high reliability (data should not be lost)
* high availability
* huge read and write volumes
* rsync (chunks, diffs)

### estimates
* 1 billion of users overall and 1 million of daily active users
* 100 files per user
* 100KB per file

### storage estimates
* 1 billion * 100 * 100KB = 10PB

### 
