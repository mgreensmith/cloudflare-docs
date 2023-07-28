---
pcx_content_type: concept
title: How KV works
weight: 7
---

# How KV works

Workers KV is a global, low-latency, key-value data store. It stores data in a small number of centralized data centers, then caches that data in Cloudflare's data centers after access. KV supports exceptionally high read volumes with low latency, making it possible to build highly dynamic APIs and websites that respond as quickly as a cached static file would. While reads are periodically revalidated in the background, requests which are not in cache and need to hit the centralized back end can see high latencies.

## Write data to KV and read data from KV

When you write to KV, your data is written to central data stores. It is not sent automatically to every location’s cache, but regional tiers are notified within seconds to do a purge of that key.

![Your data is written to central data stores when you write to KV.](/images/kv/kv-write.svg)

Initial reads from a location do not have a cached value. The data must be read from the nearest regional tier, followed by a central tier, degrading finally to the central store for a truly cold global read. While the very first access is slow globally, subsequent requests are faster, especially if they are concentrated in a single region.

![Initial reads will miss the cache and go to the nearest central data store first.](/images/kv/kv-slow-read.svg)

<!-- Frequent reads from the same location return the cached value without reading from a central data store, resulting in faster response times. -->

Frequent reads from the same location return the cached value without reading from anywhere else, resulting in the fastest response times. Workers KV operates diligently to keep the latest value in the cache by refreshing from upper tiers and the central data stores in the background. This is done carefully so that assets that are being accessed continue to be kept served from the cache without any stalls.

![As mentioned above, frequent reads will return a cached value.](/images/kv/kv-fast-read.svg)

Because Workers KV stores data centrally and uses a hybrid push/pull-based replication to store data in cache, it is generally suitable for use cases where you need to write relatively infrequently, but read quickly and frequently.

Workers KV is optimized for high-read applications. Workers KV reach its full performance when data is being read frequently.
Infrequently read values are pulled from other data centers or the central store, while more popular values are cached in the data centers they are requested from.

## Performance

To improve Workers KV performance, increase the [`cacheTTL`](/kv/learning/kv-performance-optimizations/#optimize-get-long-tail-performance) parameter up from its default 60s. 

Workers KV achieves this performance by caching which makes reads eventually-consistent with writes. 

Changes are usually immediately visible in the Cloudflare global network location at which they are made. Changes may take up to 60 seconds or more to be visible in other global network locations as their cached versions of the data time out or for them to see reads to trigger a refresh. 

Negative lookups indicating that the key doesn't exist are also cached, so the same delay exists noticing a value is created as when a value is changed.

Workers KV is not currently ideal for situations where you need support for atomic operations or where values must be read and written in a single transaction.

If you need stronger consistency guarantees, consider using [Durable Objects](/durable-objects/).

Refer to [Notice updated values within seconds](/kv/learning/kv-performance-optimizations/#notice-updated-values-within-seconds) if you need finer-grained guarantees about the behavior of concurrent writes into KV.

KV does not perform like an in-memory datastore, such as [Redis](https://redis.io). Accessing KV values, even when locally cached, has significantly more latency than reading a value from memory within a Worker script.

## Consistency

KV achieves this performance by being eventually-consistent. Changes are usually immediately visible in the Cloudflare global network location at which they are made but may take up to 60 seconds or more to be visible in other global network locations as their cached versions of the data time out. 

Visibility of changes takes longer in locations which have recently read a previous version of a given key (including reads that indicated the key did not exist, which are also cached locally). 

Workers KV is not ideal for situations where you need support for atomic operations or where values must be read and written in a single transaction.

If you need stronger consistency guarantees, consider using [Durable Objects](/durable-objects/). One pattern is to send all of your writes for a given KV key through a corresponding instance of a Durable Object, and then read that value from KV in other Workers. This is useful if you need more control over writes, but are satisfied with KV's read characteristics described above.

KV does not perform like an in-memory datastore, such as [Redis](https://redis.io). Accessing KV values, even when locally cached, has significantly more latency than reading a value from memory within a Worker script.

Refer to [KV performance optimizations](/kv/learning/kv-performance-optimizations/) to learn more about performance optimizations technique.

## Security

All values are encrypted at rest with 256-bit AES-GCM, and only decrypted by the process executing your Worker scripts or responding to your API requests.



