raftdb
======

raftdb is a simple distributed key value store based on the Raft consensus protocol. It can be run on Linux, OSX, and Windows.

## Running raftdb
*Building raftdb requires Go 1.9 or later. [gvm](https://github.com/moovweb/gvm) is a great tool for installing and managing your versions of Go.*

Starting and running a raftdb cluster is easy. Download raftdb like so:
```bash
mkdir -p $GOPATH/src/github/hanj4096
cd $GOPATH/src/github/hanj4096
git clone github.com/hanj4096/raftdb
```

Build raftdb like so:
```bash
cd $GOPATH/src/github.com/hanj4096/raftdb
go install ./...
```

#### Step 1: Modify the /etc/hosts File

Add your servers’ hostnames and IP addresses to each cluster server’s /etc/hosts file (the hostnames below are representative).

```
<Meta_1_IP> raft-cluster-host-01
<Meta_2_IP> raft-cluster-host-02
<Meta_3_IP> raft-cluster-host-03
```



> Verification steps:
>
> Before proceeding with the installation, verify on each server that the other servers are resolvable. Here is an example set of shell commands using ping:
>
> ping -qc 1 raft-cluster-host-01
>
> ping -qc 1 raft-cluster-host-02
>
> ping -qc 1 raft-cluster-host-03

We highly recommend that each server be able to resolve the IP from the hostname alone as shown here. Resolve any connectivity issues before proceeding with the installation. A healthy cluster requires that every meta node can communicate with every other meta node.

### Step 2: Bring up a cluster
Run your first raftdb node like so:
```bash
$GOPATH/bin/raftd -id node01  -haddr raft-cluster-host-01:8091 -raddr raft-cluster-host-01:8089 ~/.raftdb
```

Let's bring up 2 more nodes, so we have a 3-node cluster. That way we can tolerate the failure of 1 node:
```bash
$GOPATH/bin/raftd -id node02 -haddr raft-cluster-host-02:8091 -raddr raft-cluster-host-02:8089 -join raft-cluster-host-01:8091 ~/.raftdb

$GOPATH/bin/raftd -id node03 -haddr raft-cluster-host-03:8091 -raddr raft-cluster-host-03:8089 -join raft-cluster-host-01:8091 ~/.raftdb
```

## Reading and writing keys
You can now set a key and read its value back:
```bash
curl -X POST raft-cluster-host-01:8091/key -d '{"foo": "bar"}' -L
curl -X GET raft-cluster-host-01:8091/key/foo -L
```

You can now delete a key and its value:
```bash
curl -X DELETE raft-cluster-host-01:8091/key/foo -L
```

### Three read consistency level
You can now read the key's value by different read consistency level:
```bash
curl -X GET raft-cluster-host-01:8091/key/foo?level=stale
curl -X GET raft-cluster-host-01:8091/key/foo?level=default  -L
curl -X GET raft-cluster-host-01:8091/key/foo?level=consistent  -L
```


