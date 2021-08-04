# RateLimiterTheory
This describes how a rate limiter can be implemented using redis
This implements the Token Bucket Algorithm 


## Data Structures to be used:

1. A key - value pair for every user to its hashkey. 
    user:ID -> hashKey 
2. A hash with 2 fields, bucketSize and timeStamp. 
    hashkey -> bucketSize value
               timeStamp value
               
## Initialization

When a new client is registered a entry is made in the user:ID and a hashkey is set for it.

`set user:42 456`

Create a hash with setting the bucketSize field to 10 and timeStamp field to current timestamp.

`hset 456 bucketSize 10 timeStamp 100620` 


## Refill part

Get the current timestamp 100630. 
For the current user 42, the refilling will be done. At first the hashkey is obtained

`get user:42`
Output: `"456"`

Now get the timestamp field value: 
`hget 456 timeStamp` 

Now ghet the bucketSize
`hget 456 bucketSize` 

The rate in which the bucket needs to be filled is 1/6 tokens per second.
So now current bucketSize is: min( 10, old_bucketsize + ((current_timestamp - old_timestamp)/6))
The result is rounded off to the closest integer

Then, the bucketSize is updated and the timestamp

`hset 456 timeStamp 100630 bucketSize 5`

## Serving part

When a client comes, at first refill part is done. 

After that, 

`hincrby 456 bucketSize -1`

If the output is positive, the user is served. Otherwise it is throttled and bucketSize is reset to 0.




