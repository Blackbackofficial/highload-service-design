# HighLoad github
## 1. Topic and target audience
### Topic
GitHub
Service Type: Version Control System (VCS)
### MVP functionality
- Authorization flow
- Work with the repository (create / store / download / make changes)
- Working with Pull Request (creating/receiving)
- Work with Issues (create / receive)
- Working with comments in pull requests and searches (create/receive)

### The target audience
- According to information from official reports, we will take `80+ million`
- Daily active users `24 million`
- The number of repositories `> 330 million` (Let's take one of this number. As of November 2018, 100m repositories were created in Github. Taking into account that in total for the period 2018-2020 the number of repositories increased by another 100m (2019 - + 40m; 2020 - +60m, 2021 - +70m), assume that at the moment `number of repositories = 330+ million`)

#### Geographic distribution
- North America - 35%
- Asia - 30%
- Europe - 25%
- The rest - 10%

## 2. Load calculation
### Product metrics
As of 2021, the monthly number of users is 32m people. At that time, the number of users was approximately 73m.
Then with the current audience of 80m+ people, let's assume that `monthly number of users = 600m.`
And let's take `daily number of active users` equal to `~30+ million people`.

Let's assume that on average one user has ~5 repositories. Repositories exceeding 50Mb in size are rare, so let's assume that the average maximum repository size is ~40Mb, and one user has no more than one such repositories. The remaining repositories have a size not exceeding ~2MB.
Then we take the average `size of one repository equal to 9.6 Mb`

In addition, in one repository there is approximately:
- `5 Pull Request ~ 100 Bytes`\
  Out of 80+m users, 70% create Pull Requests for 50% of existing repositories. From the personal experience of colleagues and mine, I decided to take that one user creates 1 Pull Request per week. Then (80 * 1000000 * 0.7 * 48) / (250 * 1000000 * 0.5) = 5.3 ~ 5
- `4 Issues ~ 512 Bytes (Text size of one issue ~ 128 Bytes)`\
  Out of 80+m users, 30% create Issues on 50% of the existing repositories. From the personal experience of colleagues and mine, I decided to take that one user creates 2 Issues per month. Then (80 * 1000000 * 0.3 * 24) / (250 * 1000000 * 0.5) = 4.2 ~ 4
- `10 comments to Pull Requests and issues'am ~ 650 Bytes` (Let's take an average comment length of 65 characters)

Let's also assume that 60% of users have an avatar in their profile and its size is ~0.5Mb.

Then it turns out that `Average user storage size = ~ 50.5 MB`

#### Average number of user actions by type for the period.

| Action type                                            | Quantity/Period |
|--------------------------------------------------------|-----------------|
| Creating a repository                                  | 2 per month     |
| Downloading a repository (git clone)                   | 4 per month     |
| Download new data from the repository (git pull)       | 3 per day       |
| Adding changes to the repository (git commit/git push) | 5 per day       |
| Create an issue                                        | 2 per month     |
| Getting an issue                                       | 1 per week      |
| Adding a comment                                       | 2 per day       |
| Receiving comments                                     | 2 per day       |
| Creating a Pull Request                                | 1 per week      |
| Receiving a pull request                               | 1 per day       |


### Technical metrics
#### Storage size by data type
1. Repositories = 330,000,000 * 9.6 MB = 3021.24 TB
2. Issues = 330,000,000 * 4 * 128 Bytes = 168.9 GB
3. Comments = 330,000,000 * 10 * 65 Bytes = 214.5 GB
4. Pull Requests = 330,000,000 * 5 * 20 Bytes = 33 GB
5. User photos = 80,000,000 * 0.6 * 0.5 Kb = 24 GB

#### Network traffic and RPS
```
The coefficient of conversion of user actions for the period in RPS:
r = 32,000,000 / 24 * 60 * 60 = 370.37 people/sec
```

1. Create a repository

| Method | Transfered over network, Kb | RPS                 | Traffic   |
|--------|-----------------------------|---------------------|-----------|
| POST   | 3,9                         | 2 * r / 30 = `23,1` | 90,1 Kb/s |

2. Downloading the repository (git clone)

Authorization check

| Method | Transfered over network, Kb | RPS                 | Traffic  |
|--------|-----------------------------|---------------------|----------|
| GET    | 0,16                        | 4 * r / 30 = `46,3` | 7,4 Kb/s |

Getting Meta Data

| Method | Transfered over network, Kb | RPS                 | Traffic   |
|--------|-----------------------------|---------------------|-----------|
| GET    | 0,3                         | 4 * r / 30 = `46,3` | 13,9 Kb/s |

Downloading the repository

| Method | Transfered over network, Kb | RPS                 | Traffic  |
|--------|-----------------------------|---------------------|----------|
| GET    | 10240                       | 4 * r / 30 = `46,3` | 463 Mb/s |

3. Download new data from the repository (git pull)

Authorization check

| Method | Transfered over network, Kb | RPS              | Traffic    |
|--------|-----------------------------|------------------|------------|
| GET    | 0,16                        | 3 * r = `1041,6` | 166,7 Kb/s |

Getting Meta Data

| Method | Transfered over network, Kb | RPS              | Traffic    |
|--------|-----------------------------|------------------|------------|
| GET    | 0,3                         | 3 * r = `1041,6` | 312,5 Kb/s |

Download new data from the repository

| Method | Transfered over network, Kb | RPS              | Traffic  |
|--------|-----------------------------|------------------|----------|
| GET    | 3,3                         | 3 * r = `1041,6` | 3,3 Mb/s |

4. Adding changes to the repository (git commit/git push)

Authorization check

| Method | Transfered over network, Kb | RPS            | Traffic    |
|--------|-----------------------------|----------------|------------|
| GET    | 0,16                        | 5 * r = `1736` | 277,8 Kb/s |

Sending Meta Data

| Method | Transfered over network, Kb | RPS            | Traffic    |
|--------|-----------------------------|----------------|------------|
| POST   | 0,3                         | 5 * r = `1736` | 520,8 Kb/s |

Download new data from the repository

| Method | Transfered over network, Kb | RPS            | Traffic  |
|--------|-----------------------------|----------------|----------|
| POST   | 3,3                         | 5 * r = `1736` | 5,6 Mb/s |

5. Create an issue

| Method | Transfered over network, Kb | RPS                 | Traffic   |
|--------|-----------------------------|---------------------|-----------|
| POST   | 3,4                         | 2 * r / 30 = `23,1` | 78,5 Kb/s |

6. Getting issues

| Method | Transfered over network, Kb | RPS                | Traffic  |
|--------|-----------------------------|--------------------|----------|
| GET    | 36,4                        | 2 * r / 7 = `99,2` | 3,5 Mb/s |

7. Adding a comment

| Method | Transfered over network, Kb | RPS             | Traffic   |
|--------|-----------------------------|-----------------|-----------|
| POST   | 18,9                        | 2 * r = `694,4` | 12,8 Mb/s |

8. Receiving comments

| Method | Transfered over network, Kb | RPS             | Traffic   |
|--------|-----------------------------|-----------------|-----------|
| GET    | 95,5                        | 2 * r = `694,4` | 64,8 Mb/s |

9. Create a Pull Request

| Method | Transfered over network, Kb | RPS            | Traffic    |
|--------|-----------------------------|----------------|------------|
| POST   | 3,4                         | r / 7 = `49,6` | 168,6 Kb/s |

10. Receiving a pull request

| Method | Transfered over network, Kb | RPS          | Traffic   |
|--------|-----------------------------|--------------|-----------|
| GET    | 46,6                        | r  = `347,2` | 15,8 Mb/s |

##### Total
| Action type                                            | RPS       | Traffic     |
|--------------------------------------------------------|-----------|-------------|
| Authorization check                                    | 2823,9    | 451,9 Kb/s  |
| Getting Meta Data                                      | 1087,9    | 326,38 Kb/s |
| Sending Meta Data                                      | 1736      | 520,8 Kb/s  |
| Creating a repository                                  | 23,1      | 90,1 Kb/s   |
| Downloading a repository (git clone)                   | 46,3      | 463 Mb/s    |
| Download new data from the repository (git pull)       | 1041,6    | 3,3 Mb/s    |
| Adding changes to the repository (git commit/git push) | 1736      | 5,6 Mb/s    |
| Create an issue                                        | 23,1      | 78,5 Kb/s   |
| Getting issues                                         | 99,2      | 3,5 Mb/s    |
| Adding a comment                                       | 694,4     | 12,8 Mb/s   |
| Receiving comments                                     | 694,4     | 64,8 Mb/s   |
| Creating a Pull Request                                | 49,6      | 168,6 Kb/s  |
| Receiving a pull request                               | 347,2     | 15,8 Mb/s   |
|                                                        |           |             |
| **Total values**                                       | `10401,7` | `0,56 Gb/s` |
| **At peak load, take x3**                              | `31205,1` | `1,68 Gb/s` |

## Database
### Logic diagram
![logical](/img/logical.png)
### Physical layout
![physical](/img/physical_new.png)

### Sharding
- **PostgreSQL Repos** - sharding by `user_id`
- **PostgreSQL PRs** - sharding by `(repo_id, pull_request_id)`
- **PostgreSQL Issues** - sharding by `(repo_id, issue_id)`
- **MinIO** - sharding by `repo_id`
- **Ceph** - sharding by `user_id`

### Size calculation
#### PostgreSQL
| Table          | Value                                                          | Total         |
|----------------|----------------------------------------------------------------|---------------|
| Users          | (8 + 64 + 64 + 256) bytes * 80,000,000 people                  | 31.4 Gb       |
| Repositories   | (8 + 8 + 256 + 4) bytes * 330,000,000 repositories             | 84,8 Gb       |
| Pull Requests  | (8 + 8 + 8 + 4) bytes * 30,000,000 people * 48 times a year    | 37,5 Gb/year  |
| Issues         | (8 + 8 + 8 + 256) bytes * 30,000,000 people * 24 times a year  | 187,7 Gb/year |
| Comments (all) | (8 + 8 + 8 + 256) bytes * 30,000,000 people * 730 times a year | 5,5 Gb/year   |

#### MinIO
| Table     | Value                            | Total |
|-----------|----------------------------------|-------|
| Packfiles | 10 Mb * 300,000,000 repositories | 3 Pb  |

#### Ceph
| Table   | Value                            | Total |
|---------|----------------------------------|-------|
| Avatars | 80,000,000 people * 0,6 * 0.5 Kb | 24 Gb |

#### Redis
| Table    | Value                                 | Total   |
|----------|---------------------------------------|---------|
| Sessions | 30 000 000 people * (8 + 64 + 8) byte | 2,25 Gb |

## Technology
### Client
- In choosing technologies for the front, we will build on the knowledge of the developers in our team.
- To distribute the frontend, we will use `CDN` to reduce the delay.
- To resolve domain names (domain name resolving) take a highly available DNS service, for example `Amazon Route 53`
- Also, to filter traffic and prevent high loads and DDoS attacks, we will add another service-firewall `CloudFlare`

### Balancing
- Instead of RoundRobin balancing, we will use Geo-DNS balancing. This will ensure the lowest latency for users from different regions + users can balance to remote servers only if both data centers fail in the nearest region.
- For balancing at the Nginx level, we will use L7 balancing
- To balance between database replicas, we will use L4 balancing

### Server side
- We will use a microservice architecture that will allow easy scaling, as well as control the load on services.
- The main language in microservices will be `Golang`, as it supports parallelism out of the box and is very fast, productive. The only problem with `Golang` is the presence of `garbage collector`, which can affect performance. In the future, you can think about transferring the most loaded services to `Rust`.
- Functional distribution of services:
    - `git-auth` - authorization service. Works with `Redis`
      | Service    | RPS         | Traffic         |
      | ---------- | ----------- | -------------- |
      | `git-auth` | 8472        | 1,32 Mb/s      |
    - `git-core` - service for working with users + the main service through which interaction with other microservices takes place
      | Service    | RPS         | Traffic         |
      | ---------- | ----------- | -------------- |
      | `git-core` | 31205       | 1,68 Gb/s      |
    - `git-meta` - a service that works with PostgreSQL and basic features not related to git flow (PRs, Issues, etc.)
      | Service    | RPS         | Traffic         |
      | ---------- | ----------- | -------------- |
      | `git-meta` | 14266       | 98 Mb/s        |
    - `git-pack` - service that works with `packfiles`
      | Service    | RPS         | Traffic         |
      | ---------- | ----------- | -------------- |
      | `git-pack` | 8472        | 1,38 Gb/s      |
- For monitoring we will use `prometheus/grafana`
- To manage traffic (timeouts, retries, load balancing) and ensure fault tolerance when communicating between services, we will use `istio` in the k8s cluster
### Repositories
- To store most of the data, we will use the PostgreSQL relational DBMS, it is highly reliable and will be enough to create new data in it
- To speed up the write, apply sharding to withstand the write load
- In addition, to store objects (packfiles and avatars), we will use object storage: Ceph for avatars and MinIO for packfiles
- Ceph - free open source distributed file system, devoid of bottlenecks and single points of failure, which is an easily scalable cluster of nodes
- MinIO is also object storage, but has a number of advantages, such as compatibility with the Amazon S3 API and the size of the stored data up to 5 TB. In addition, this tool is used by GitLab. Unfortunately, this storage is paid, so it was decided to use a free solution for avatars.
- For replication in Postgres we will use `patroni`
- We will use `Redis` to store user sessions

## Architecture
![arch](/img/arch.jpg)
## Servers
### Location
Taking into account the geographical distribution of the server audience, we will arrange it as follows:
![regions](/img/regions.jpeg)

### Selection of configurations
All calculations are made for one data center. To ensure fault tolerance in each region there will be 2 data centers - the main and the backup.
####k8s
Since we provide Kubernetes with a cluster of nodes that it can use to run containerized tasks and specify how much CPU and memory (RAM) each container needs, Kubernetes can place containers on nodes in a way that makes the most efficient use of resources.
Therefore, we will indicate the total number of resources that we will provide Kubernetes. Services require large enough RAM for caches and application speed and resource availability, and a CPU with more cores to improve performance.
According to the table in the etcd documentation on the [CoreOS] website(https://coreos.com/etcd/docs/latest/v2/admin_guide.html), it is recommended to have an odd number of members in the cluster. In order for the cluster to continue working after the failure of one master node, we take 3.
##### A bit about caches
Judging by the RPS, the most frequently received data is the data of the repository + PR. We will store, for example, avatars + repositories data in caches, because they will be updated the least often. As a caching strategy, I would choose SNLRU or 2Q. Roughly speaking, with a division into hot / warm / cold caches. Warm and cold caches are invalidated by themselves (for example, with a fixed queue size of 2Q, rarely used caches crash). Hot caches of repositories can be invalidated by events (for example, git push - the structure of files in the repository changes - the cache has been updated).

| Region   | Instance | RAM    | CPU      | Storage     |
|----------|----------|--------|----------|-------------|
| American | 5 nodes  | 256 Gb | 64 cores | 256 Gb NVMe |
| European | 5 nodes  | 256 Gb | 64 cores | 256 Gb NVMe |
| African  | 5 nodes  | 256 Gb | 64 cores | 256 Gb NVMe |
| Asiatic  | 5 nodes  | 256 Gb | 64 cores | 256 Gb NVMe |

#### PostgreSQL Repos
The size of the stored data Users + Repos ~ 90 Gb. These tables have a fairly large number of RPS, we will increase the access speed through a large number of shards and an increased number of cores. Let's also take 96 Gb of RAM for caching.

| Region   | Instance                                     | RAM   | CPU      | Storage               |
|----------|----------------------------------------------|-------|----------|-----------------------|
| American | 5 shards + 2 replicas (sync+async) per shard | 96 Gb | 64 cores | 2 NVMe x 2 TB RAID 10 |
| European | 5 shards + 2 replicas (sync+async) per shard | 96 Gb | 64 cores | 2 NVMe x 2 TB RAID 10 |

#### PostgreSQL PRs
The size of the stored data PRs + PRs Comments (half of all) ~ 2.9 TB. RPS for these tables is small, so we will leave 2 shards. Due to the large size, we will take 8 NVMe x 2 TB

| Region   | Instance                                     | RAM   | CPU      | Storage               |
|----------|----------------------------------------------|-------|----------|-----------------------|
| American | 2 shards + 2 replicas (sync+async) per shard | 96 Gb | 64 cores | 8 NVMe x 2 TB RAID 10 |
| European | 2 shards + 2 replicas (sync+async) per shard | 96 Gb | 64 cores | 8 NVMe x 2 TB RAID 10 |

#### PostgreSQL Issues
The size of the stored data PRs + PRs Comments (half of all) ~ 3 TB. Set up the same way as with PRs

| Region   | Instance                                     | RAM   | CPU      | Storage               |
|----------|----------------------------------------------|-------|----------|-----------------------|
| American | 2 shards + 2 replicas (sync+async) per shard | 96 Gb | 64 cores | 8 NVMe x 2 TB RAID 10 |
| European | 2 shards + 2 replicas (sync+async) per shard | 96 Gb | 64 cores | 8 NVMe x 2 TB RAID 10 |

#### MinIO
According to [MinIO tuning guidelines](https://min.io/resources/docs/CPG-MinIO-implementation-guide.pdf) and from [MinIO benchmark results](https://blog.min.io/scaling-minio -more-hardware-for-higher-scale/) we can conclude that for our purposes, a configuration of 32 nodes with 8 HDD clusters, each containing 4 TB HDDs, is suitable. Let's take 12 machines providing x2 storage size compared to the available ones.

| Region   | Instance | RAM   | CPU      | Storage                          |
|----------|----------|-------|----------|----------------------------------|
| American | 12       | 96 Gb | 24 cores | 24 nodes x 8 NVMe x 4 TB RAID 10 |
| European | 12       | 96 Gb | 24 cores | 24 nodes x 8 NVMe x 4 TB RAID 10 |
| African  | 12       | 96 Gb | 24 cores | 24 nodes x 8 NVMe x 4 TB RAID 10 |
| Asiatic  | 12       | 96 Gb | 24 cores | 24 nodes x 8 NVMe x 4 TB RAID 10 |

#### Ceph
The calculated size of the photos turned out to be small ~ 24 Gb. Therefore, two machines with 2 NVMe x 2 TB will be enough

| Region   | Instance | RAM   | CPU      | Storage       |
|----------|----------|-------|----------|---------------|
| American | 2        | 96 Gb | 24 cores | 2 NVMe x 2 TB |

#### Redis
The average session storage size that was calculated earlier = 2.25 Gb. Average number of RPS ~ 8500. 8 processor cores are enough for this. For access speed, we will divide it into two shards.

| Region   | Instance                        | RAM   | CPU     | Storage    |
|----------|---------------------------------|-------|---------|------------|
| American | 2 shards + 2 replicas per shard | 32 Gb | 8 cores | 64 Gb NVMe |
| European | 2 shards + 2 replicas per shard | 32 Gb | 8 cores | 64 Gb NVMe |

#### Nginx (L7 balancer)
Since the frontend will be distributed from a CDN, 64 GB of NVMe will be enough. For access speed, we will locate nginx in each region.

| Region   | Instance                | RAM   | CPU      | Storage    |
|----------|-------------------------|-------|----------|------------|
| American | 3 main + 3 additional   | 32 Gb | 24 cores | 64 Gb NVMe |
| European | 3 main + 3 additional   | 32 Gb | 24 cores | 64 Gb NVMe |
| African  | 3 main + 3 additional   | 32 Gb | 24 cores | 64 Gb NVMe |
| Asiatic  | 3 3 main + 3 additional | 32 Gb | 24 cores | 64 Gb NVMe |

#### L4 Postgres balancer
Let's place the balancers in the same regions as the Postgres servers themselves.

| Region   | Instance              | RAM   | CPU      | Storage    |
|----------|-----------------------|-------|----------|------------|
| American | 1 main + 2 additional | 32 Gb | 24 cores | 64 Gb NVMe |
| European | 1 main + 2 additional | 32 Gb | 24 cores | 64 Gb NVMe |

#### L4 Redis balancer
Since the number of RPS for Redis is almost 2 times less than for Postgres, we can reduce the number of cores to 16.

| Region   | Instance              | RAM   | CPU      | Storage    |
|----------|-----------------------|-------|----------|------------|
| American | 1 main + 2 additional | 32 Gb | 16 cores | 64 Gb NVMe |
| European | 1 main + 2 additional | 32 Gb | 16 cores | 64 Gb NVMe |

## Sources
- https://octoverse.github.com/
- https://octoverse.github.com/2019/
- https://github.blog/2018-11-08-100M-repos/
- https://blog.min.io/scaling-minio-more-hardware-for-higher-scale/
  https://amazinghiring.com/searching-for-developers-on-github/#:~:text=GitHub%20is%20the%20largest%20web,(e.g.%20Ruby%20on%20Rails).
- https://github.blog/2009-10-20-how-we-made-github-fast/
- https://about.gitlab.com/handbook/engineering/infrastructure/production/architecture/
- https://croit.io/blog/ceph-performance-test-and-optimization
- https://min.io/resources/docs/CPG-MinIO-implementation-guide.pdf
