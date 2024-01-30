## Introduction

Back-of-the-envelope Estimation means to broadly estimate system capacity or performance requirements.

## Power of Two

The table of power of 2 is used when calculating data volume in our system, which can become quite enormous. It is important to remember two things: **A byte is a sequence of 8 bits, and an ASCII characters requires 1 byte of memory**.

| Power | Approximate value (bytes) | Full name  | Short name |
| ----- | ------------------------- | ---------- | ---------- |
| 10    | 1 Thousand                | 1 Kilobyte | 1 KB       |
| 20    | 1 Million                 | 1 Megabyte | 1 MG       |
| 30    | 1 Billion                 | 1 Gigabyte | 1 GB       |
| 40    | 1 Trillion                | 1 Terabyte | 1 TB       |
| 50    | 1 Quadrillion             | 1 Petabyte | 1 PB       |

## Latency Numbers

Some conclusions according to the tables below:

- Using memory is fast, using disk is slow (avoid seeking disk)
- Simple compression algorithms are fast
- Compress data before sending over the internet
- Sending data between data centers can be slow

| Operation name                                   | Time                    |
| ------------------------------------------------ | ----------------------- |
| L1 cache reference                               | 0.5 ns                  |
| Branch mispredict                                | 5 ns                    |
| L2 cache reference                               | 7 ns                    |
| Mutex lock/unlock                                | 100 ns                  |
| Main memory reference                            | 100 ns                  |
| Compress 1K bytes with Zippy                     | 10,000 ns = 10 µs       |
| Send 2K bytes over 1 Gbps network                | 20,000 ns = 20 µs       |
| Read 1 MB sequentially from memory               | 250,000 ns = 250 µs     |
| Round trip within the same datacenter            | 500,000 ns = 500 µs     |
| Disk seek 10,000,000                             | ns = 10 ms              |
| Read 1 MB sequentially from the network          | 10,000,000 ns = 10 ms   |
| Read 1 MB sequentially from disk                 | 30,000,000 ns = 30 ms   |
| Send packet CA (California) -> Netherlands -> CA | 150,000,000 ns = 150 ms |

| Notes                                               |
| --------------------------------------------------- |
| ns = nanosecond, µs = microsecond, ms = millisecond |
| 1 ns = 10^-9 seconds                                |
| 1 µs= 10^-6 seconds = 1,000 ns                      |
| 1 ms = 10^-3 seconds = 1,000 µs = 1,000,000 ns      |

## Availability Numbers

A system availability is measured in nines, the more nines the better.

| Availability % | Downtime per day    | Downtime per week   | Downtime per month | Downtime per year |
| -------------- | ------------------- | ------------------- | ------------------ | ----------------- |
| 99%            | 14.40 minutes       | 1.68 hours          | 7.31 hours         | 3.65 days         |
| 99.99%         | 8.64 seconds        | 1.01 minutes        | 4.38 minutes       | 52.60 minutes     |
| 99.999%        | 864.00 milliseconds | 6.05 seconds        | 26.30 seconds      | 5.26 minutes      |
| 99.9999%       | 86.40 milliseconds  | 604.80 milliseconds | 2.63 seconds       | 31.56 seconds     |

## Example

Twitter:

- 300 million monthly active users.
- 50% of users use Twitter daily.
- Users post 2 tweets per day on average.
- 10% of tweets contain media.
- Data is stored for 5 years.

Estimations:

- Daily active users: 300 million \* 50% = 150 million
- Tweets per day: 150 million \* 2 = 300 million
- Tweets per day with media = 300 million \* 10% = 30 million

- Queries:

  - Queries per second (QPS): 300 million / 24 hours / 3600 seconds in an hour = approx. 3500
  - Peak QPS: 2 \* QPS = 7000

- Storage (total tweet size):

  - 64 characters id = 64 bytes
  - 140 characters tweet = 140 bytes
  - media = 1 MB
  - Total per day = 300 million \* 204 bytes + 30 million \* 1 MB = approx. 30 TB per day
  - Total in 5 years = 30 TB \* 5 years \* 365 days in a year = approx. 55 PB

## Tips

1. Approximate calculation results, no need to be exact
2. Write down assumptions
3. Label units
4. Common asked estimations are: QPS, peak QPS, storage, cache, and number of servers.
