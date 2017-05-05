# section2

5. 更新



通过http api能对service配置文件增删改查，如果更新完成后，可以通过signup命令来生效



7. 组建集群

一个consul agent就是一个独立的程序。一个长时间运行的守护进程，运行在concul集群中的每个节点上。



启动一个consul agent ，只是启动一个孤立的node，如果想知道集群中的其他节点，应该将consul agent加入到集群中去 cluster。



agent有两种模式：server与client。server模式包含了一致性的工作：保证一致性和可用性（在部分失败的情况下），响应RPC，同步数据到其他节点代理。



client 模式用于与server进行通信，转发RPC到服务的代理agent，它仅保存自身的少量一些状态，是非常轻量化的东西。本身是相对无状态的。



agent除去设置server/client模式、数据路径之外，还最好设置node的名称和ip。



一张经典的consul架构图片：







LAN gossip pool包含了同一局域网内所有节点，包括server与client。这基本上是位于同一个数据中心DC。



WAN gossip pool一般仅包含server，将跨越多个DC数据中心，通过互联网或广域网进行通信。



Leader服务器负责所有的RPC请求，查询并相应。所以其他服务器收到client的RPC请求时，会转发到leader服务器。





第一，没有必要配置客户端与服务器的地址; 发现是自动完成的。 第二，检测节点故障的工作不放置在服务器上，但被分布。 这使得故障检测比天真的心跳方案更具扩展性。 第三，它是作为一个消息层通知时，重要事件，如leader选举举行。





安装vagrant， sudo vagrant init 初始化vagrant环境。



vagrant up 启动一个虚拟node节点



vagrant status 查看vm启动的状态，包括vm的名称



vagrant ssh vm\_name 登陆到vm节点





bootstrap的模式，该模式node可以指定自己作为leader，而不用进行选举。然后再依次启动其他server，配置为非bootstrap的模式。最后把第一个serverbootstrap模式停止，重新以非bootstrap模式启动，这样server之间就可以自动选举leader。



分别在两个vm上配置consul agent，如



$ vagrant ssh n1



vagrant@n1:~$ consul agent -server -bootstrap-expect 1 \

    -data-dir /tmp/consul -node=agent-one -bind=172.20.20.10



$ vagrant ssh n2

vagrant@n2:~$ consul agent -data-dir /tmp/consul -node=agent-two \

    -bind=172.20.20.11

这个时候，应用consul members 进行查询，两个consul node分别是独立，没有什么关联。





将client加入到server 集群中



vagrant@n1:~$ consul join 172.20.20.11



再用consul members查询，就发现多了一个node节点。





这样手动



加入新节点太麻烦，而较好的方法就将节点配置成自动加入集群



 consul agent -atlas-join \

  -atlas=ATLAS\_USERNAME/infrastructure \

  -atlas-token="YOUR\_ATLAS\_TOKEN"





离开集群



ctrl+c，或者 kill 指定的agent进程，就可以将相关的agent推出集群





让consul 运行起来。consul server推荐至少在3~5个之间，推荐的方法是一开始启动其中一台server，并且配置到bootstrap的模式，该模式node可以指定自己作为leader，而不用进行选举。然后再依次启动其他server，配置为非bootstrap的模式。最后把第一个serverbootstrap模式停止，重新以非bootstrap模式启动，这样server之间就可以自动选举leader。





http://www.bubuko.com/infodetail-800623.html





8. 查询健康状态



 curl http://localhost:8500/v1/health/state/critical    // 应用http接口查询失败的节点

对于失败的节点，应用DNS查询时，是无法拿到返回结果的

dig @127.0.0.1 -p 8600 web.service.consul



9. K/V存储

consul还提供了键/值存储的功能。

如 查询 所有K/V

curl -v http://localhost:8500/v1/kv/?recurse



保存键为web/key2, flags 为42， 值为true的记录。

curl -X PUT -d 'test' http://localhost:8500/v1/kv/web/key2?flags=42

true



删除记录：

curl -X DELETE http://localhost:8500/v1/kv/web/sub?recurse



更新值：

curl -X PUT -d 'newval' http://localhost:8500/v1/kv/web/key1?cas=97

true



更新index：

curl "http://localhost:8500/v1/kv/web/key2?index=101&wait=5s"



结果：\[{"CreateIndex":98,"ModifyIndex":101,"Key":"web/key2","Flags":42,"Value":"dGVzdA=="}\]





更详细的consul命令详解：



http://m.oschina.net/blog/353392



10. 断电恢复outage recover

当有一台服务器不可用时，处理的方法有：

1. 对服务器进恢复，然后重新上线

2. 用新服务器，替代旧的consul服务器

这两种方式都需要将服务器ip与原来的ip相同。



3. 添加新的服务器，而ip无需与原来的相同。步骤： 停掉所有的consul服务器，将损坏的服务器ip从raft/peer.json中移除，重启其他服务器，并将新的服务器加入集群。



