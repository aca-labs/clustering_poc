# clustering

`src/clustering.cr` defines an abstract module if you wish to include a clustering component to your class.
Implement `stabilize(nodes : Array(NamedTuple(ip: String, port: Int32)))` with your required cluster stabilization logic.
Once a cluster has stabilized, a message with the version is published to a redis channel defined by `Clustering.redis_version_channel`.

Running `$ shards build` will create a simple proof of concept app, run it with `./bin/poc`

## Implementation

A single etcd lease is granted per node so if a node drops out of the cluster,
all associated key/values will expire from the cluster.

### Watchfeeds

There are 4 etcd watchfeeds

- election: notify nodes still in cluster that there is no longer a leader
- readiness: propagate readiness against a cluster version
- version: set by leader when there's a change in cluster state, nodes must 'ready' themselves to this signal
- discovery: keeps track of nodes in etcd under a service namespace

## Dependencies

- etcd `~> v3.3.13`
- redis `~> v5`
