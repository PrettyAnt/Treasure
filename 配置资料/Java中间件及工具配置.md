# Java中间件及工具配置 

- docker常见的命令 *后文中的/Users/chenyu/JavaLearn  替换成自己的路径*
    - `docker`   显示docker的命令
    - `docker -c`  显示config
    - `docker -v`  显示版本
    - `docker -d`  debug
    - `docker images` 查看镜像
    - `docker ps` 查看启动的镜像服务
    - `docker stop xxx`  停止服务
    - `docker rm xxx` 删除
- redis
    - redis安装&命令
        - `docker search redis` 查看redis的版本
        - `docker pull redis:latest` 拉取最新版的redis
        - **latest**: 可以替换成版本号
        - `docker run -itd --name redis-test -p 6379:6379 redis`   运行容器
            - **-itd**
            - **i**:以交互模式运行容器，通常与-t同时使用
            - **t**:为容器重新分配一个伪输入终端，通常与-i同时使用
            - **d**:后台运行容器，并返回容器ID
            - **--name redis-test**:启动的实例的名称 
            - **-p 6379:6379**：内部到外部端口的映射。映射容器服务的 6379 端口到宿主机的 6379 端口。外部可以直接通过宿主机ip:6379 访问到 Redis 的服务。
        - `docker exec -it redis-test /bin/bash`  连接测试使用redis服务*或者docker exec -ti redis-test sh，然后输入redis-cli***keys命令一般不在生产环境使用**
            - `redis-cli`redis 命令行客户端
            - `set xxx 1`
            - `sadd myset a b c d e`set集合
            - `smembers myset`列出集合数据
            - `set get`
            - `del xxx`
            - `keys *`列出所有的key
            - `keys x*`匹配x,通配符
            - `keys x??`匹配固定个数?,通配符
            - `keys x[h-l]`匹配x开头，落在[h-l]之间的key
            - `dbsize`计算key总数
            - `exists x`判断某个key是否存在
            - `expire key seconds`key在seconds秒后过期
            - `ttl key`查看key剩余的过期时间(time to live)-2表示key已经不存在了,-1表示key存在，并且没有过期时间
            - `persist key`去掉key的过期时间
            - `exit 退出`
            - `redis-server`redis服务器
            - `redis-benchmark`Redis-Cluster(v3.0)支持分布式
            - `redis-check-aof`AOF文件修复工具
            - `redis-check-dump`RDB文件修复工具
            - `redis-sentinel`Sentinel服务器(2.8以后)
    - redis常用配置
        - daemonize是否守护进程(no|yes)
        - portRedis对外端口号
        - logfileRedis日志系统
        - dirRedis工作目录
    - Redis 特点
        - 开源
        - 高性能Key-Value服务器
        - 多种数据结构
        - 丰富的功能
        - 高可用分布式支持
    - Redis 应用场景
        - 缓存系统
        - 计数器
        - 消息队列系统
        - 排行榜
        - 社交网络
        - 实时系统
    - 支持的数据类型
        - String
        - Hash
        - List
        - Set
        - Sorted Set
    - Redis 特性
        - 1、速度快
            - 数据存在内存
            - 非阻塞IO
            - 使用C语言写的
            - 线程模型为单线程
        - 2、持久化(断电不丢数据)
            - Redis所有数据保持在内存中，对数据的更新将异步地保存在磁盘上
        - 3、多种数据结构
            - BitMaps:位图
            - HyperLogLog:超小内存唯一值计数
            - GEO:地理信息定位
        - 4、支持多种客户端语言
            - Java
            - Php
            - Python
            - Ruby
            - Lua
            - nodejs
        - 5、功能丰富
            - 发布订阅
            - Lua脚本
            - 事物
            - pipeline
        - 6、简单
            - 不依赖外部库
            - 单线程模型
        - 7、主从复制
        - 8、高可用、分布式
            - Redis-Sentinel(v2.8)支持高可用
            - Redis-Cluster(v3.0)支持分布式 
- rocketmq
    - rocketmq安装
        - `docker search rocketmq` 查找rocketmq版本
        - `docker pull rocketmq:latest` 拉取最新版的rocketmq
        - **latest**：可以替换成版本号
        - 需要准备相应的三个脚本，部署主要是nameserver、broker
            - nameserver:无状态节点，接受broker的token信息注册，为producer、consumer提供路由信息。NameServer 是整个 recketmq 的“大脑” ，它是 rocketmq 的服务注册中心，所以 romqetmq 需要先启动 nameserver 再启动 rocket 中的 broker。
                - nameserver安装:`docker run -d -p 9876:9876 -v /Users/chenyu/JavaLearn/rocketmq/install/namesrv/logs:/root/logs -v /Users/chenyu/JavaLearn/rocketmq/install/namesrv/store:/root/store --name rmqnamesrv -e "MAX_POSSIBLE_HEAP=100000000" apache/rocketmq sh mqnamesrv`
            - broker:是rocketmq消息的中转角色，负责存储消息、转发消息，一般把他称为server。主要作用是接收来自 Producer 的消息并存储，Consumer 从这里取得消息。
                - broker安装:`docker run -d -p 10911:10911 -p 10909:10909 -v /Users/chenyu/JavaLearn/rocketmq/install/broker/logs:/root/logs -v /Users/chenyu/JavaLearn/rocketmq/install/broker/store:/root/store -v /Users/chenyu/JavaLearn/rocketmq/install/broker/broker.conf:/opt/rocketmq/conf/broker.conf --name rmqbroker --link rmqnamesrv:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" apache/rocketmq sh mqbroker -c /opt/rocketmq/conf/broker.conf`tips:可以在-v前回车，然后替换本地路径。**broker.conf文件中的brokerIP1需要改成本机的地址**
            - dashbook：监控平台
                - 控制台面板的安装:`docker run -d --name rmqbroker --link rmqnamesrv:namesrv -e "JAVA_OPTS=-Drocketmq.config.namesrvAddr=namesrv:9876 -Drocketmq.config.isVIPChannel=false" -p 9090:8080 -t --name rmConsole styletang/rocketmq-console-ng`端口映射需要保持统一如："9876",控制台端口为了防止冲突，可以替换成别的，如9090
    - 消息中间件(MessageQueue,MQ)
        - 主要功能:异步
        - 常见的MQ:ActivityMQ、RabbitMQ、Kafka、RocketMQ
        - 使用场景
            - 异步与解耦
            - 流量削峰
            - 数据分发
    - rocketmq中的概念
        - 分组(goroup)
        - 主题(Topic)
        - 消息队列(Message Queue)
        - 偏移量(Offsent)
    - 普通消息的消费模式
        - 集群消费
        - 广播消费
    - 普通消息的发送
        - 同步发送
        - 异步发送
        - 单向发送
- consul
    - consul安装
        - `docker search consul` 查找consul版本
        - `docker search --no-trunc consul` 查找consul版本(详细显示description)
        - `docker pull consul:latest` 拉取最新版的consul
        - `docker stop consul` 停止consul
        - `docker rm consul` 删除consul镜像
        - `docker run -d --name=consul -p 8500:8500 -ke CONSUL_BIND_INTERFACE=eth0 consul`启动consul镜像
    - 服务注册: 服务进程在服务中心注册自己的位置。他通常注册自己的主机和端口号，有时还有身份验证信息，协议，版本号，以及运行环境的详细资料
    - 服务发现: 客户端应用进程向注册中心发起查询，来获取服务的位置。服务发现的一个重要功能就是提供一个可用的服务列表
    - consul的优势
        - 使用Raft算法来保证唯一性，比复杂的Paxos算法更直接，相比较而言，zookeeper采用的是Paxos,而etcd使用的则是Raft
        - 支持多数据中心，内外网的服务采用不同的端口进行监听。数据中心集群可以避免但数据中心的单点故障，而其部署则需要考虑网络延迟，分片情况等。zookeeper和etcd均不提供多数据中心功能的支持。
        - 支持健康检查。etcd不提供此功能。
        - 支持http和dns协议接口。zookeeper的集成较为复杂，etcd只支持http协议。
        - 官方提供web管理页面，etcd无此功能。
        - 综合比较，consul作为服务注册和配置管理的新星，比较值得关注和研究。
    - consul 特性
        - 服务发现:consul提供了通过DNS或者HTTP接口的方式来注册服务和发现服务。 一些外部的服务通过Consul很容易的找到它所依赖的服务。 
        - 健康检查:consul的Client可以提供任意数量的健康检查，既可以与给定的服务相关 联(“webserver是否返回200 OK”)，也可以与本地节点相关联(“内存利用率是否低于90%”)。操作员可以使用 这些信息来监视集群的健康状况，服务发现组件可以使用这些信息将流量从不健康的主机路由出去。 
        - key/value存储:应用程序可以根据自己的需要使用Consul提供的Key/Value存储。 Consul提供了简单易 用的HTTP接口，结合其他工具可以实现动态配置、功能标记、领袖选举等等功能。 
        - 安全服务通信:consul可以为服务生成和分发TLS证书，以建立相互的TLS连接。意图可用于定义允许哪 些服务通信。服务分割可以很容易地进行管理，其目的是可以实时更改的，而不是使用复杂的网络拓扑和静 态防火墙规则。 
        - 多数据中心:consul支持开箱即用的多数据中心. 这意味着用户不需要担心需要建立额外的抽象层让业务 扩展到多个区域。
    - consul 工作原理
        - 当 Producer 启动的时候，会向 Consul 发送一个 post 请求，告诉 Consul 自己的 IP 和 Port。 
        - Consul 接收到 Producer 的注册后，每隔10s(默认)会向 Producer 发送一个健康检查的请求，检验 Producer是否健康。
        - 当 Consumer 发送 GET 方式请求 /api/address 到 Producer 时，会先从 Consul 中拿到一个存储服务IP 和 Port 的临时表，从表中拿到 Producer 的 IP 和 Port 后再发送 GET 方式请求 /api/address。
        - 该临时表每隔10s会更新，只包含有通过了健康检查的 Producer。 
- elasticsearch
    - elasticsearch安装
        - `docker search elasticsearch` 查找elastic版本
        - `docker pull elasticsearch:7.17.9` 拉取elasticsearch 这里需要注意，版本号需要和kibana的版本保持一致
        - `docker run --name elasticsearch -p 9200:9200 -p 9300:9300 -e  "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" -v /Users/chenyu/JavaLearn/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /Users/chenyu/JavaLearn/elasticsearch/data:/usr/share/elasticsearch/data -v /Users/chenyu/JavaLearn/elasticsearch/plugins:/usr/share/elasticsearch/plugins -d elasticsearch:7.17.9 ` elasticsearch 启动脚本
        - elasticsearch配置文件内容:`http.host: 0.0.0.0`,文件名elasticsearch.yml
    - elasticsearch 插件安装
        - docker searh elasticsearch-head
        - docker run -d --name es-head -p 9100:9100 docker.io/mobz/elasticsearch-head:5
        - docker run -d --name=es-head -p 9100:9100 mobz/elasticsearch-head:5
        - elasetic-search
        - 容器没有vi vim 命令解决办法
        - apt-get update
        - apt-get install vim
    - elasticsearch概念
        - 使用java语言开发的一套开源的全文搜索引擎
        - 用于搜索、日志管理、安全分析、指标分析、业务分析、应用性能监控等多个领域
        - 底层基于Lucene开源库开发，提供restAPI，可以被任意语言调用
        - 支持基于分布式部署，可水平扩展
        - 更新迭代快、社区活跃、文档丰富
    - elasticsearch集群
        - 集群部署使其可以随时可用和并按需求扩容，并保证数据的安全性
        - 通过启动参数cluster.name修改集群名称，默认名称为elasticsearch
    - 节点(Node)
        - 一个节点是一个java进程实例，一台机器可以运行多个实例，一般情况下一台机器只运行一个节点
        - 一个集群有一个或多个节点
        - 通过启动参数node.name 定义节点名称
        - 每个节点都保存了集群的状态信息，只有Master节点可以修改集群的状态信息
        - 集群状态信息包括:所有节点信息、索引、Mapping、Setting、分片路由等信息
    - 索引(Index)
        - 一个集群下面可以新建多个索引，索引体现了逻辑空间概念
        - 索引是一类相似文档的集合，是文档的容器，类比关系型数据库中的一张表的Schema的概念
        - 每个索引有自己的Mapping用于定义文档的字段名和字段类型
        - 每个索引有自己的Settings用于定义不同的数据分布，也就是索引使用分片的情况
    - 分片(Shard)
        - 分片是物理空间概念，索引中的数据都分布在分片上
        - 一个分片就是运行的一个Lucence的实例
        - 分片分为主分片和副分片，一般主分片和副分片应该分布在不同的节点上
        - 主分片用于解决数据水平扩展的问题，主分片的数目在索引创建后指定，后续不允许修改(number*of*shards)
        - 副分片用来解决数据高可用问题，是主分片的拷贝，数量可以动态调整(number*of*replicas)
        - 分片数的设定
            - 分片数设置过小，影响后续水平扩展，单个分片数据量太大导致数据重新分配耗时
            - 分片数设置过大，影响搜索结果的相关性打分，影响搜索结果数据准确性
            - 分片数设置过大，导致单个节点上会有过多的分片，资源浪费，浪费性能
    - 文档(Document)
        - 文档是所有可搜索数据的最小单位，类似关系数据库中某张表中的一行记录
        - 文档会被序列成JSON格式，JSON对象由字段组成
        - 每个字段都有对应的字段类型，类型可以自己指定，也可以使用ElasticSearch自动推算
        - JSON文档支持数组和嵌套
        - 每个文档都有一个唯一ID，可以自己指定，也可以系统自动生成
        - 一个文档主要的元信息如下:
            - \_index:文档所属的索引名
            - \_type:文档所属的类型名
            - \_id:文档的唯一ID
            - \_source:文档存储的Json数据
            - \_version:文档的版本信息
            - \_source:相关性打分
    - 映射(Mapping)
        - 映射是用于定义ES对索引中字段的存储类型、分词方式和是否存储等信息，就像数据库中的Schema,描述了文档可能具有的字段或属性、每个字段的数据类型
        - 只不过关系型数据库建表时必须指定字段类型，而ES对于字段类型可以不指定然后动态对字段类型猜测，也可以在创建索引时具体指定字段的类型。
        - 对字段类型根据数据格式自动识别的映射称之为动态映射(Dynamic Mapping)，我们创建索引时具体定义字段类型的映射称之为静态映射或显示映射(Explicit Mapping)
    - 倒排索引
        - ElasticSearch 使用一种称为“倒排索引”的结构，他适用于快速的全文搜索，一个倒排索引由文档中所有不重复词的列表构成，对于其中每个词，有一个包含它的文档列表，这样可以通过单词快速的找到其所在的文档:
            - 倒排索引包含两部分：单词词典(Team Dictionary)和倒排列表(Posting List)
            - 单词词典记录单词到倒排列表的关联关系，一般通过B+树或者哈希链表实现
            - 倒排列表记录单词对应的文档结合，由倒排索引项组成
            - 倒排索引项由文档ID(docId),词频(term frequencies),单词位置(term position)，偏移量(character offsets)组成
            - ElasticSearch中默认会对文档中的每个字段做倒排索引可以强行指定不对某些字段设置倒排索引
    - 分词器(Analyzer)
        - Analysis是把全文本转换为一系列单词的过程，也叫分词
        - Analysis通过Analyzer分词器实现，可以使用ElasticSearch内置的分词器或者定制化分词器
        - 在写入文档和搜索查询时都用到分词器
        - 在写入文档数据时，需要对TEXT字段做分词然后建立倒排索引
        - 在搜索查询时，需要对输入的查询语句进行分词，然后通过倒序索引进行搜索
        - Analyzer分词器的组成L
            - Character Filters:对原始文本进行预处理，比如去除HTML，字符串替换，正则匹配替换
            - Tokenizer:按照规则切分单词ES内置的有whitespace/standard/uax*url*email/pattern/keyword/path hierarchy等Tokenizer
            - Token Filter:对切分的单词进行加工，例如小写转换(Lowercase)，删除stopwords(stop)，增加同义词(synonym)等
- kibana
    - `docker search kibana` 查找kibana版本
    - `docker pull kibana:7.17.9` 拉取kibana版本号和elasticsearch保持一致 
    - `docker run --name=kibana -p 5601:5601 --restart=always  -m 512m  --memory-swap=1024m -v /Users/chenyu/JavaLearn/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml -d kibana:7.17.9`kibana启动脚本
    - kibana配置文件内容:`http.host: 0.0.0.0`,文件名kibana.yml
- mysql
    - `docker search mysql` 查找mysql版本
    - `docker pull mysql:5.7`拉取mysql，实际开发中版本需要保持一致
    - `docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7`启动mysql
    - `docker exec -ti mysql /bin/bash` 进入mysql并测试
        - `mysql -h localhost -u root -p` host、user、pwd
        - `show databases;` 查看数据库注意尾行分号和空格
        - `create database test;` 创建数据库test
        - `use test;` 使用数据库‘use 库名’
        - `create table t1( id int not null, name char(20));` 创建一张表
        - `show tables;` 查看表
        - 下载可视化软件查看，如:DBeaver
- 事务seata
    - 事务概念
        - 事务ACID原则
            - 原子性(Atomicity):事务中的所有操作，要么全部成功，要么全部失败
            - 一致性(Consistency):要保证数据库内部完整性约束、声明性约束
            - 隔离性(Isolation):对同一资源操作的事务不能同时发生
            - 持久性(Durability):对数据库做的一切修改将永久保存，不管是否出现故障
    - Seata安装
        - `docker search seata` 查找seata版本
        - `docker pull seataio/seata-server:1.4.2`下载seata 命令见官网
        - `docker run --name seata-server -p 8091:8091 -d  seataio/seata-server:1.4.2`  运行容器
        - `docker cp seata-server:/seata-server /Users/chenyu/JavaLearn/seata/config/seata-server`  将启动容器里面的seata-server拷贝到本地指定的目录
            - /Users/chenyu/JavaLearn/seata/config/seata-server/resources/file.conf今后可能需要修改的配置文件
            - /Users/chenyu/JavaLearn/seata/config/seata-server/resources/registry.conf今后可能需要修改的配置文件
        - 一些命令
            - docker ps 查看镜像
            - docker exec -ti c9153acf6436 /bin/sh  执行seata
            - ls 文件夹列表
            - ls -l 文件夹列表(列表方式展开)
            - cd "文件夹名" 打开某个文件夹
            - cd ../resources(文件夹名)/  打开到上一级目录
    - Seata概念
        - Seata是一款开源的分布式事务解决方案，致力于在微服务架构下提供高性能和简单易用的分布式事务服务。在Seata开源之前，其内部版本在阿里系内部一直扮演着应用架构层数据一致性的中间件角色，帮助经济体平稳的度过历年的双11，对上层业务进行了有利的技术支撑。2019年开源。官网:[http://seata.io/](http://seata.io/)
    - Seata的特点
        - 微服务框架支持
            - 目前已支持Dubbo、Spring Cloud、Sofa-RPC、Motan和gRPC框架，其他框架持续集成中
        - AT模式
            - 提供无侵入自动补偿的事务模式，目前支持MySQL、Oracle、PostgreSQL、TiDB和MariaDB、H2、DB2、SQLServer、达梦开发中
        - TCC模式
            - 支持TCC模式并许可与AT混用，灵活度更高
        - SAGA模式
            - 为长事务提供有效的解决方案，提供编排式与注解式(开发中)
        - XA模式
            - 支持已实现XA接口的数据库的XA模式，目前已支持MySQL、Qracle、TiDB和MariaDB
        - 高可用
            - 支持计算分离集群模式，水平扩展能力强的数据库和Redis存储模式Raft模式Preview阶段
    - 框架特色
        - TC(Transaction Coordinator)-事务协调者:维护全局和分支事务的状态,驱动全局事务提交或回滚
        - TM(Transaction Manager)-事务管理器:定义全局事务的范围,开始全局事务、提交或回滚全局事务
        - RM(Resource Manager)-资源管理器:管理分支事务处理的资源,与TC并驱动分支事务提交或回滚
            - 其中,TC为单独部署的Server服务端,T
- consul
    - 服务注册、服务发现概念
        - 服务注册:服务进程在注册中心注册自己的位置。它通常注册自己的主机和端口号，有时还有身份验证信息，协议，版本号，以及运行环境的详细资料。
        - 服务发现:客户端应用进程向注册中心发起查询，来获取服务的位置。服务发现的一个重要作用就是提供一个可用的服务列表
    - consul介绍
        - Consul 是 HashiCorp 公司推出的开源工具，是分布式的、高可用的、 可横向扩展的用于实现分布式系统的服务发现与配置。 
        - 与其他分布式服务注册与发现的方案，Consul 的方案更“一站式”，内置了服务注册与发现框架、分布一致性协议实现、健康检查、Key/Value 存储、多数据中心方案，不再需要依赖其他工具(比如 ZooKeeper 等)，使用起来也较为简单。 
        - Consul 使用 Go 语言编写，因此具有天然可移植性(支持Linux、windows和Mac OS X)。 安装包仅包含一个可执行文件，方便部署，与 Docker 等轻量级容器可无缝配合。 
    - consul优势
        - 使用 Raft 算法来保证一致性, 比复杂的 Paxos 算法更直接. 相比较而言, zookeeper 采用的是 Paxos, 而 etcd 使用的则是 Raft。 
        - 支持多数据中心，内外网的服务采用不同的端口进行监听。 多数据中心集群可以避免单数据中心的单点故障,而其部署则需要考虑网络延迟, 分片等情况等。 zookeeper 和 etcd 均不提供多数据中心功能的支 持。 
        - 支持健康检查。 etcd 不提供此功能。
        - 支持 http 和 dns 协议接口。 zookeeper 的集成较为复杂, etcd 只支持 http 协议。 
        - 官方提供 web 管理界面, etcd 无此功能。
        - 综合比较, Consul 作为服务注册和配置管理的新星, 比较值得关注和研究。
    - consul特性
        - 服务发现(Service Discovery):Consul提供了通过DNS或者HTTP接口的方式来注册服务和发现服务。 一些外部的服务通过Consul很容易的找到它所依赖的服务。 
        - 健康检查(Health Checking):Consul的Client可以提供任意数量的健康检查，既可以与给定的服务相关 联(“webserver是否返回200 OK”)，也可以与本地节点相关联(“内存利用率是否低于90%”)。操作员可以使用 这些信息来监视集群的健康状况，服务发现组件可以使用这些信息将流量从不健康的主机路由出去。 
        - Key/Value存储:应用程序可以根据自己的需要使用Consul提供的Key/Value存储。 Consul提供了简单易 用的HTTP接口，结合其他工具可以实现动态配置、功能标记、领袖选举等等功能。 
        - 安全服务通信:Consul可以为服务生成和分发TLS证书，以建立相互的TLS连接。意图可用于定义允许哪 些服务通信。服务分割可以很容易地进行管理，其目的是可以实时更改的，而不是使用复杂的网络拓扑和静 态防火墙规则。 
        - 多数据中心:Consul支持开箱即用的多数据中心. 这意味着用户不需要担心需要建立额外的抽象层让业务 扩展到多个区域。 
    - 工作原理
        - 当 Producer 启动的时候，会向 Consul 发送一个 post 请求，告诉 Consul 自己的 IP 和 Port。
        - Consul 接收到 Producer 的注册后，每隔10s(默认)会向 Producer 发送一个健康检查的请求，检验 Producer是否健康。
        - 当 Consumer 发送 GET 方式请求 /api/address 到 Producer 时，会先从 Consul 中拿到一个存储服务 IP 和 Port 的临时表，从表中拿到 Producer 的 IP 和 Port 后再发送 GET 方式请求 /api/address。 4、该临时表每隔10s会更新，只包含有通过了健康检查的 Producer。 
- Getway
    - 什么是API网关
        - API 网关是一个处于应用程序或服务(提供 REST API 接口服务)之前的系统，用来管理授权、访问控制和流量限制等，这样 REST API 接口服务就被 API 网关保护起来,对所有的调用者透明。 
        - 因此，隐藏在 API 网关后面的业务系统就可以专注于创建和管理服务，而不用去处理这些策略性的 基础设施。
    - Getway介绍
        - Spring Cloud Gateway 是Spring Cloud的一个全新的API网关项目，目的是为了替换掉Zuul1，它基于 Spring5.0 + SpringBoot2.0 + WebFlux(基于高性能的Reactor模式响应式通信框架Netty，异步非阻塞模 型)等技术开发，性能高于Zuul，官方测试，Spring Cloud Gateway是Zuul的1.6倍 ，旨在为微服务架构 提供一种简单有效的统一的API路由管理方式。 
            - 可以与Spring Cloud Discovery Client(如Consul)、Ribbon(负载均衡)、Hystrix(登录器)等组件配合使用，实现路由转发、 负载均衡、熔断、鉴权、路径重写、日志监控等 
            - Gateway还内置了限流过滤器，实现了限流的功能。
            - 设计优雅，容易拓展
    - 核心概念
        - Route(路由):路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言 为true则匹配该路由，目标URI会被访问。 
        - Predicate(断言):这是一个java 8的Predicate，可以使用它来匹配来自HTTP请求的任何内容，如:请 求头和请求参数。断言的输入类型是一个ServerWebExchange。 
        - Filter(过滤器):指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者后对 请求进行修改。 
    - 核心流程
        - Gateway Client 向 Spring Cloud Gateway 发送请求
        - 请求首先会被 HttpWebHandlerAdapter 进行提取组装成网关上下文
        - 然后网关的上下文会传递到 DispatcherHandler ，它负责将请求分发给 RoutePredicateHandlerMapping
        - RoutePredicateHandlerMapping 负责路由查找，并根据路由断言判断路由 是否可用
        - 如果过断言成功，由 FilteringWebHandler 创建过滤器链并调用
        - 通过特定于请求的 Fliter 链运行请求，Filter 被虚线分隔的原因是Filter可以在 发送代理请求之前(pre)和之后(post)运行逻辑
        - 执行所有pre过滤器逻辑。然后进行代理请求。发出代理请求后，将运行 “post”过滤器逻辑
        - 处理完毕之后将 Response 返回到 Gateway 客户端 
- nacos
    - docker pull nacos/nacos-server:v2.1.2
    - docker run -p 8848:8848 -p 9848:9848 -p 9849:9849 --name nacos-temp -d nacos/nacos-server:v2.1.2
    - 创建conf和logs文件夹 
        - mkdir -p /docker/nacos/conf
        - mkdir -p /docker/nacos/logs
    - 复制conf和logs文件夹
        - docker cp nacos-temp:/home/nacos/logs/ /docker/nacos/logs
        - docker cp nacos-temp:/home/nacos/conf/ /docker/nacos/conf
- drools规则引擎
    - 规则引擎概念:规则引擎由推荐引擎发展而来，是一种嵌入在应用程序中的组件，实现了将业务决策从应用程序中分离出来，并使用预定义的语义模块编写业务决策。接受数据输入，解释业务规则，并根据业务规则做出业务决策。
    - 注意:需要注意的是规则引擎并不是一个具体的技术框架，而是指一类系统，即业务规则管理系统。目前市面上具体的规则引擎产品有:drools、VisualRules、iLog等
    - 官网:[https://www.drools.org](https://www.drools.org)
    - 源码地址:[https://github.com/kiegroup/drools](https://github.com/kiegroup/drools)
    - 规则引擎应用场景
        - 风险控制系统:风险贷款、风险评估
        - 反欺诈项目:银行贷款、征信验证
        - 决策平台系统:财务计算
        - 促销平台系统:满减、打折、加价购
    - drools概念:
        - Drools是一款基于Java语言开发的开源规则引擎技术，可以将复杂且多变的业务逻辑从硬编码中脱离出来，以规则脚本的形式存放在文件或数据库中。从而使得不需要重启服务器就可以生效，达到动态业务。
        - Drools具有一个易于访问企业策略、易于调整以及管理的开源业务。规则引擎，符合业内标准，速度快、效率高。业务分析师或审核人员可以利用它来轻松查看业务规则，从而检测已编码的规则是否执行了所需的业务规则。其前身是Codehaus的一个开源项目叫Drools，后被纳入JBoss门下，更名为JBoss Rules,成为了JBoss应用服务器的规则引擎。
    - Drools API开发步骤:
        - 获取KieServices
        - 获取KieContainer
        - 开启KieSessioon
        - Insert fact
        - 触发规则
        - 关闭KieSession
    - Drools规则引擎的构成:
        - Working Memory 工作内存
        - Rule Base 规则库
        - Inference Engine 推理引擎
            - Pattern Matcher(匹配器) 具体匹配哪一规则，由这个完成
            - Agenda 议程
            - Execution Engine 执行引擎
    - Drools规则引擎执行过程
        - 1、将初始数据(fact)输入至工作内存(Working Memory)
        - 2、使用Pattern Matcher 将规则库中的规则(rule)和数据(fact)比较
        - 3、如果执行规则存在冲突(confilct),即同时激活了多个规则，将冲突的规则放入冲突集合
        - 4、解决冲突，将激活的规则按顺序放入Agenda
        - 5、执行Agenda中的规则，重复步骤2~5，直到执行完毕Agenda中的所有规则
- linux命令脚本
    - ll  文件列表(可以看出是不是文件夹)
    - ls 文件列表(横向排列)
    - ls -l 文件列表(竖着排列)
    - tail -100f xxx(文件名)  如:tail -100f log4j2.file.properties,显示日志/文件的最后100行
    - cat xxx.txt(文件名) 查看文件，一次性全部显示
    - more xxx.txt(文件名) 查看文件，分页查看可以配合斜杆使用用来查询，斜杆后输入查询的词即可，如:/hello；然后按下n查询
    - vi xxx.txt(文件名),编辑文件，
        - i:进入输入模式，
        - exit:退出输入模式
        - 退出输入模式后，连按2次d,删除整行
        - shift+: 再输入wq,退出编辑并且保存
    - cp xxx.txt  xxx.txt.bk20230304 拷贝文件
    - touch xxx.txt 新建文件
    - mkdir xxx新建文件夹
    - cd "文件夹名" 打开某个文件夹
    - cd ../xxx/  退到到上一层目录，并打开xxx目录。即相当于 cd ..加上cd xxx
    - rm -f xxx.txt  强制删除文件
    - rm -r xxx  删除文件夹和子文件
    - mv xxx.txt aaa 将文件xxx.txt移动到aaa目录下，aaa表示文件夹路劲，如:../aaa/
    - zip aaa.zip xxx.txt  压缩命令
- flowable
    - 概念
        - Flowable 是一个使用Java编写的轻量级业务流程引擎。基于BPMN2.0协议，实现对流程的创建、查询、流转、报结等操作的管理。
        - Flowable项目提供了一组核心的开源业务流程引擎，这些引擎紧凑且高效。他们为开发人员。系统管理员和业务用户提供一个工作流和业务流程管理(BPM)平台。它的核心是一个非常快速且通过测试的动态BPM流程引擎。它基于Apache2.0开源协议，有稳定且经过认证的社区。
        - 目前主流的工作流开源框架是Activiti/Camunda/Flowable,它们都有一个共同的祖先jbpm。先是有了jbpm4，随后出来了一个Activiti5,Activiti5经过一段时间的发展，核心人员出现分歧，又分出来一个Camunda。Activiti5发展4年左右，紧接着就出现了Flowable
    - flowable安装
        - `docker search flowable` 查找floable版本
        - `docker pull flowable/all-in-one` 拉取floable
        - `docker run -p 8080:8080 flowable/all-in-one` 启动脚本可以在docker Desktop中搜索flowable，使用官方提供的脚本
        - [http://localhost:8080/flowable-admin](http://localhost:8080/flowable-admin) 验证,官网有
    - 创建Flowable流程步骤
        - 创建流程引擎实例(ProcessEngine)
        - 部署创建一个流程定义
        - 启动一个流程实例
        - 创建一个流程任务









































