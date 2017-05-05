# 运行

以服务端形式运行consul

$  consul agent -server -bootstrap-expect 1 -data-dir /Users/didi/consul

==&gt; WARNING: BootstrapExpect Mode is specified as 1; this is the same as Bootstrap mode.

==&gt; WARNING: Bootstrap mode enabled! Do not enable unless necessary

==&gt; Starting Consul agent...

==&gt; Consul agent running!

```
       Version: 'v0.8.1'

       Node ID: '04145f71-d5c1-a469-c270-026f3911e0b1'

     Node name: 'localhost'

    Datacenter: 'dc1'

        Server: true \(bootstrap: true\)

   Client Addr: 127.0.0.1 \(HTTP: 8500, HTTPS: -1, DNS: 8600\)

  Cluster Addr: 172.24.26.61 \(LAN: 8301, WAN: 8302\)

Gossip encrypt: false, RPC-TLS: false, TLS-Incoming: false

         Atlas: &lt;disabled&gt;
```

==&gt; Log data will now stream in as it occurs:

```
2017/05/05 16:54:07 \[INFO\] raft: Initial configuration \(index=1\): \[{Suffrage:Voter ID:172.24.26.61:8300 Address:172.24.26.61:8300}\]

2017/05/05 16:54:07 \[INFO\] raft: Node at 172.24.26.61:8300 \[Follower\] entering Follower state \(Leader: ""\)

2017/05/05 16:54:07 \[INFO\] serf: EventMemberJoin: localhost 172.24.26.61

2017/05/05 16:54:07 \[INFO\] consul: Adding LAN server localhost \(Addr: tcp/172.24.26.61:8300\) \(DC: dc1\)

2017/05/05 16:54:07 \[INFO\] serf: EventMemberJoin: localhost.dc1 172.24.26.61

2017/05/05 16:54:07 \[INFO\] consul: Handled member-join event for server "localhost.dc1" in area "wan"

2017/05/05 16:54:14 \[ERR\] agent: failed to sync remote state: No cluster leader

2017/05/05 16:54:16 \[WARN\] raft: Heartbeat timeout from "" reached, starting election

2017/05/05 16:54:16 \[INFO\] raft: Node at 172.24.26.61:8300 \[Candidate\] entering Candidate state in term 2

2017/05/05 16:54:16 \[INFO\] raft: Election won. Tally: 1

2017/05/05 16:54:16 \[INFO\] raft: Node at 172.24.26.61:8300 \[Leader\] entering Leader state

2017/05/05 16:54:16 \[INFO\] consul: cluster leadership acquired

2017/05/05 16:54:16 \[INFO\] consul: New leader elected: localhost

2017/05/05 16:54:16 \[INFO\] consul: member 'localhost' joined, marking health alive

2017/05/05 16:54:19 \[INFO\] agent: Synced service 'consul'
```

生成文件夹：

$ ls

checkpoint-signature    raft

node-id            serf

查看consul服务节点

$ consul members

Node       Address            Status  Type    Build  Protocol  DC

localhost  172.24.26.61:8301  alive   server  0.8.1  2         dc1

将http请求发给consul server

$ curl localhost:8500/v1/catalog/nodes

\[{"Node":"Armons-MacBook-Air","Address":"10.1.10.38"}\]

6. 注册服务

1. 创建文件夹

$ mkdir etc

$ mkdir etc/consul.d

2. 将服务配置文件写入文件夹内

$  echo '{"service": {"name": "web", "tags": \["rails"\], "port": 80}}'  &gt;/Users/didi/consul/etc/consul.d/web.json

3. 重启consul，并将配置文件的路径给consul

$  consul agent -server -bootstrap-expect 1 -data-dir /Users/didi/consul   -config-dir /Users/didi/consul/etc/consul.d/

==&gt; WARNING: BootstrapExpect Mode is specified as 1; this is the same as Bootstrap mode.

==&gt; WARNING: Bootstrap mode enabled! Do not enable unless necessary

==&gt; Starting Consul agent...

==&gt; Consul agent running!

           Version: 'v0.8.1'

           Node ID: '04145f71-d5c1-a469-c270-026f3911e0b1'

         Node name: 'localhost'

        Datacenter: 'dc1'

4. 查询ip和端口



DNS方式：dig @127.0.0.1 -p 8600 web.service.consul SRV



Http方式：curl http://localhost:8500/v1/catalog/service/web

