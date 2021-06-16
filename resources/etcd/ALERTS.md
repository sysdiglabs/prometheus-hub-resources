# Alerts
## etcdMembersDown
etcd cluster: members are down.

## etcdInsufficientMembers
etcd cluster: insufficient members.

## etcdNoLeader
etcd cluster: member has no leader.

## etcdHighNumberOfLeaderChanges
etcd cluster: leader changes within the last 15 minutes. Frequent elections may be a sign of insufficient resources, high network latency, or disruptions by other components, and it should be investigated.

## etcdHighNumberOfFailedGRPCRequests
etcd cluster: number of requests failed on the etcd instance.

## etcdHighNumberOfFailedGRPCRequests
etcd cluster: number of requests failed on the etcd instance.

## etcdGRPCRequestsSlow
etcd cluster: gRPC requests are too slow on the etcd instance.

## etcdMemberCommunicationSlow
etcd cluster: member communication with is slow on etcd instance.

## etcdHighNumberOfFailedProposals
etcd cluster: Too many proposal failures within the last 30 minutes on etcd instance.

## etcdHighFsyncDurations
etcd cluster: 99th percentile fsync durations are too high on etcd instance.

## etcdHighCommitDurations
etcd cluster: 99th percentile commit durations are too high on etcd instance.

## etcdHighNumberOfFailedHTTPRequests
High number of requests failures on etcd instance

## etcdHTTPRequestsSlow
etcd instance HTTP requests are slow.
