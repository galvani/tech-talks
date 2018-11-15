# Redis

Redis can be persistent.

## Usecases

 * Key-Value storage
 * Hashes – Can use this to store data which can be huge key-value lookups & needs to be broken down.

 > I could imagine creating hashes with all relevant info for certain segment and filtering upon them
 Advantage would be taking load of the database and the speed.

 * Counters & Hyperlogs – Best to build **real-time dashboards** & leaderboards.

 > e.g.: We could bypass the call for lead count that happens on every insert.

 * Sorted Sets – You can use this for triggering events at a particular time, eg drip campaigns, automation of events.
 * Pub/Sub – Since version 2.0, Redis provides the capability to distribute data utilizing the Publish/Subscribe messaging paradigm.

   > Some organizations have moved to Redis and away from other message queuing systems (i.e., RabbitMQ, zeromq) due to Redis’s simplicity and performance.
 * Bitmaps – Good to build cohorts and other analysis.
    (https://medium.com/hacking-and-gonzo/bitmapist-analytics-and-cohorts-for-redis-44be43458ef6)
 * Queues
 * Whole page caching
 
## Features

### Memcache features comparison

|   **Feature**               |   Memcached   |   Redis |
| --- | --- | --- |
| Sub-millisecond latency | Yes | Yes |
Developer ease of use | Yes 	| Yes
Data partitioning 	| Yes 	| Yes
Support for a broad set of programming languages 	| Yes 	| Yes
Advanced data structures 	- 	| Yes
Multithreaded architecture 	| Yes |	-
Snapshots |	- | Yes
Replication | -	| Yes
Transactions | - | Yes
Pub/Sub 	| - | Yes
Lua scripting |	- | Yes
Geospatial support 	| -	| Yes

### LUA

> Lua is a powerful, efficient, lightweight, embeddable scripting language. It supports procedural programming, object-oriented programming, functional programming, data-driven programming, and data description.

Lua could replace segment selects, parts of segments could be converted to LUA. It is an alternative to MySQL
functions and triggers.

> see: https://www.compose.com/articles/a-quick-guide-to-redis-lua-scripting/

## Uses **igbinary**

Igbinary is a drop in replacement for the standard php serializer. Instead of the time and space consuming textual representation used by PHP's serialize, igbinary stores php data structures in a compact binary form.

## Redis Sentinel

Redis Sentinel provides high availability for Redis. In practical terms this means that using Sentinel you can create a Redis deployment that resists without human intervention to certain kind of failures.

## Redis Data Types

 * Binary-safe strings.
 * Lists
  > collections of string elements sorted according to the order of insertion. They are basically linked lists.
 * Sets:
  > collections of unique, unsorted string elements.
 * Sorted sets
  > similar to Sets but where every string element is associated to a floating number value, called score. The elements are always taken sorted by their score, so unlike Sets it is possible to retrieve a range of elements (for example you may ask: give me the top 10, or the bottom 10).
 * Hashes
  > which are maps composed of fields associated with values. Both the field and the value are strings. This is very similar to Ruby or Python hashes.
 * Bit arrays (or simply bitmaps)
  > it is possible, using special commands, to handle String values like an array of bits: you can set and clear individual bits, count all the bits set to 1, find the first set or unset bit, and so forth.

### Lists

Redis lists are implemented via Linked Lists.
This means that even if you have millions of elements inside a list, the operation of adding a new element in the head or in the tail of the list is performed in constant time. The speed of adding a new element with the LPUSH command to the head of a list with ten elements is the same as adding an element to the head of list with 10 million elements.

They are very useful for proxessing queues. For example if we used redis list for page_hit tracking; the response would be almost instant comparing to current implementation

We might:
 * store hit for further processing in redis
 * use worker to do all the reqiuired work done now

Advantage would be great speed for user, we low load. Controlled processing (no spikes).

```
> rpush mylist A
(integer) 1
> rpush mylist B
(integer) 2
> lpush mylist first
(integer) 3
> lrange mylist 0 -1
1) "first"
2) "A"
3) "B"
```


### Redis Keys

Anything binary can be a key, they are binary safe, there is though a way to structure the key

`"object-type:id"` as lead:12

### Commands

 * SET/GET - MSET/MGET
 * DELETE/EXISTS



## Mautic use

 * Segments would not have to be queried in loop, we could fetch them all at once and let redis page it
 * Queue for inserting new contacts into segments
 * Segments may be translated to LUA query
 * We could completely remove page_hits and similar tables and migrate the to redis, which would cause great
 speed improvements in multiple actions

### Credits

https://pepipost.com/blog/prefer-redis-others/
https://redis.io/topics/sentinel
https://redislabs.com/solutions/use-cases/redis-timeseries-data/
https://www.credera.com/blog/technology-insights/java/redis-explained-5-minutes-less/