# etcd

Etcd, the Distributed Key-Value Store
https://thenewstack.io/about-etcd-the-distributed-key-value-store-used-for-kubernetes-googles-cluster-container-manager/

1. version check
```
$ curl -L http://127.0.0.1:2379/version
etcd 2.0.10

$ curl -L http://etcd0:2379/version
etcd 2.0.10

$ curl -L http://etcd1:2379/version
etcd 2.0.10

$ curl -L http://etcd2:2379/version
etcd 2.0.10
```

2. health check
```
# curl -L http://etcd0:2379/health
{"health": "true"}

# curl -L http://etcd1:2379/health
{"health": "true"}

# curl -L http://etcd2:2379/health
{"health": "true"}
```

3. Leader check
```
$ curl -L http://etcd0:2379/v2/stats/leader
{"message":"not current leader"}

$ curl -L http://etcd1:2379/v2/stats/leader
{"message":"not current leader"}

$ curl -L http://etcd2:2379/v2/stats/leader
{
  "leader":"d282ac2ce600c1ce",
  "followers": {
    "ade526d28b1f92f7": {
      "latency": {
        "current":0.6359,
        "average":0.48021724137931066,
        "standardDeviation":0.7161043404510907,
        "minimum":0.0915,
        "maximum":7.3409
      },
      "counts": {
        "fail":0,
        "success":2146
      }
    },
    "cf1d15c5d194b5c9": {
      "latency": {
        "current":2.9305,
        "average":0.45694259086672906,
        "standardDeviation":0.6950079606063815,
        "minimum":0.0703,
        "maximum":10.1547
      },
      "counts": {
        "fail":0,
        "success":2146
      }
    }
  }
}
```

https://poweruphosting.com/blog/etcd-tutorial/    
4. Put Key/Value

```
$ curl http://127.0.0.1:2379/v2/keys/msg -XPUT -d value="Hello world"
{
  "action":"set",
  "node": {
    "key":"/msg",
    "value":"Hello world",
    "modifiedIndex":7,
    "createdIndex":7
  }
}
```

5. Get the value of the key
```
$ curl http://127.0.0.1:2379/v2/keys/msg
{
  "action":"get",
  "node": {
    "key":"/msg",
    "value":"Hello world",
    "modifiedIndex":7,
    "createdIndex":7
  }
}
```

5. Change the Value of the Key
```
$ curl http://127.0.0.1:2379/v2/keys/msg -XPUT -d value="Hi Globe"
{
  "action":"set",
  "node": {
    "key":"/msg",
    "value":"Hi Globe",
    "modifiedIndex":8,
    "createdIndex":8
  },
  "prevNode": {
    "key":"/msg",
    "value":"Hello world",
    "modifiedIndex":7,
    "createdIndex":7
  }
}
```

6. Delete the Key
```
$ curl http://127.0.0.1:2379/v2/keys/msg -XDELETE
{
  "action":"delete",
  "node":{
    "key":"/msg",
    "modifiedIndex":9,
    "createdIndex":8
  },
  "prevNode":{
    "key":"/msg",
    "value":"Hi Globe",
    "modifiedIndex":8,
    "createdIndex":8
  }
}
```

7. Key TTL( Time to Live)
```
$ curl http://127.0.0.1:2379/v2/keys/msgs -XPUT -d value=hi -d ttl=5
{
  "action":"set",
  "node": {
    "key":"/msgs",
    "value":"hi",
    "expiration":"2018-11-30T08:15:43.2218308Z",
    "ttl":5,
    "modifiedIndex":10,
    "createdIndex":10
  }
}

$ curl http://127.0.0.1:2379/v2/keys/msgs
{
  "errorCode":100,
  "message":"Key not found",
  "cause":"/msgs",
  "index":11
}
```

8. unset the TTL (can update before key expire only)
```
$ curl http://127.0.0.1:2379/v2/keys/msgs -XPUT -d value=hi -d ttl= -d prevExist=true
{
  "action":"update",
  "node": {
    "key":"/msgs",
    "value":"hi",
    "modifiedIndex":13,
    "createdIndex":12
  },
  "prevNode": {
    "key":"/msgs",
    "value":"hi",
    "expiration":"2018-11-30T08:17:27.7269734Z",
    "ttl":4,
    "modifiedIndex":12,
    "createdIndex":12
  }
}
```
