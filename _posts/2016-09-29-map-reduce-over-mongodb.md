---
layout: post
title: "Map-Reduce over MongoDB"
tagline: ""
categories : [nosql]
tags: [mongodb, nosql, mapreduce]
---
{% include JB/setup %}


[TOC]

# Map-Reduce Command

MongoDB提供了`mapReduce`命令，用于执行Map-Reduce操作。

http://bens-mbp.local:62236/Dash/hjkuleqk/docs.mongodb.com/manual/_images/map-reduce.png

命令接收三个参数：Map函数、Reduce函数，查询条件

还支持可选的参数：Finalize函数

mapReduce命令的完整参数：

```javascript
db.runCommand(
               {
                 mapReduce: <collection>,
                 map: <function>,
                 reduce: <function>,
                 finalize: <function>,
                 out: <output>,
                 query: <document>,
                 sort: <document>,
                 limit: <number>,
                 scope: <document>,
                 jsMode: <boolean>,
                 verbose: <boolean>,
                 bypassDocumentValidation: <boolean>
               }
             )
```

# Map-Reduce的处理和输出

In MongoDB, map-reduce operations use custom JavaScript functions.

In MongoDB, the map-reduce operation can write results to a collection or return the results inline.

If you write map-reduce output to a collection, you can perform subsequent map-reduce operations on the same input collection that merge replace, merge, or reduce new results with previous results.

When returning the results of a map reduce operation inline, the result documents must be within the BSON Document Size limit, which is currently 16 megabytes.

# Map-Reduce with Sharding

MongoDB supports map-reduce operations on sharded collections. Map-reduce operations can also output the results to a sharded collection.

MongoDB支持在Sharded Collections上执行MR操作。也支持MR操作结果输出到Sharded Collection上。

When using sharded collection as the input for a map-reduce operation, mongos will automatically dispatch the map-reduce job to each shard in parallel. There is no special option required. mongos will wait for jobs on all shards to finish.

当在Sharded Collection上执行MR操作，mongos会自动分发MR任务到每个Shard上并行执行。

mongos会等到所有Shards任务执行完毕。

If the out field for mapReduce has the sharded value, MongoDB shards the output collection using the _id field as the shard key.

To output to a sharded collection:

1. If the output collection does not exist, MongoDB creates and shards the collection on the _id field.
2. For a new or an empty sharded collection, MongoDB uses the results of the first stage of the map-reduce operation to create the initial chunks distributed among the shards.
3. mongos dispatches, in parallel, a map-reduce post-processing job to every shard that owns a chunk. During the post-processing, each shard will pull the results for its own chunks from the other shards, run the final reduce/finalize, and write locally to the output collection.

For best results, only use the sharded output options for mapReduce in version 2.2 or later.

# Map Reduce Concurrency

The map-reduce operation is composed of many tasks, including reads from the input collection, executions of the map function, executions of the reduce function, writes to a temporary collection during processing, and writes to the output collection.

During the operation, map-reduce takes the following locks:

- The read phase takes a read lock. It yields every 100 documents.
- The insert into the temporary collection takes a write lock for a single write.
- If the output collection does not exist, the creation of the output collection takes a write lock.
- If the output collection exists, then the output actions (i.e. merge, replace, reduce) take a write lock. This write lock is global, and blocks all operations on the mongod instance.

# Perform Incremental Map-Reduce

To perform incremental map-reduce:

- Run a map-reduce job over the current collection and output the result to a separate collection.

- When you have more data to process, run subsequent map-reduce job with:
  1. the query parameter that specifies conditions that match only the new documents.
  2. the out parameter that specifies the reduce action to merge the new results into the existing output collection.

# Troubleshoot the Map Function

Define the emit function to print the key and value:

```javascript
var emit = function(key, value) {
    print("emit");
    print("key: " + key + "  value: " + tojson(value));
}
```

Invoke the map function with a single document from the collection:

```javascript
var myDoc = db.orders.findOne( { _id: ObjectId("50a8240b927d5d8b5891743c") } );
map.apply(myDoc);
```

or with multiple documents from the collection:

```javascript
var myCursor = db.orders.find( { cust_id: "abc123" } );

while (myCursor.hasNext()) {
    var doc = myCursor.next();
    print ("document _id= " + tojson(doc._id));
    map.apply(doc);
    print();
}
```