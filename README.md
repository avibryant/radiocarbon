radiocarbon
===========

Exponentially decaying metrics.

Nothing to see here yet, but the name was too good not to put a placeholder in for.

--------
Proposal: accepts tuples (over UDP?) of (group: String, key: String, timestamp: Float, value: Float). For each (group, key), keeps a sparse matrix of 10x256 decaying counters: the 10 is for different halflives (from 2^1...2^10), and the 256 are bins for a transformation of the value into an 8-bit floating point space. It's the client's responsibility to scale both timestamp and value so this makes sense.

Queries (over HTTP?) must specify a group, and can additionally restrict by key and/or halflife. They will receive the current value of all the relevant counters.

Interesting things you can do with this include:
- moving averages
- change detection (subtracting different half lives)
- slope detection (ditto)
- mean, variance, quantiles, and other values computable from the distribution (and changes/slopes of these)
- decayed unique values (have N keys in the same group for buckets of an HLL counter, compute the 99.9th percentile as the max)

----
It would be interesting to implement against a backing store which had atomic increments, using 3 atomic counters for each decaying counter - at any one time, one of them would get most of the writes, with some falling into the second, and then eventually the second and third get merged and a new one created (when it's been enough halflives that precision is in danger).
