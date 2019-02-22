<<<<<<< HEAD
 
=======
tips：
本次为第一次写，后续还会优化。
该内容主要就是为了理思路，而不是教你咋配置软件。
配置软件的文档太多了。 有什么需要，可以给我发邮件哦。
我会逐步完善的。

下载地址:
https://github.com/prometheus/
https://www.consul.io/
https://github.com/hashicorp/consul-template
https://github.com/grafana/grafana

该文档时版本:
prometheus-2.7.0-rc.2.linux-amd64
node_exporter-0.17.0.linux-amd64
alertmanager-0.16.0.linux-amd64
grafana-5.4.3-1.x86_64.rpm
consul_1.4.2_linux_amd64
consul-template_0.20.0_linux_amd64

本来想写安装文档了的。后来想想放弃了。网上关于安装的内容真的太多了。
我这边就整理一些思路和踩过的坑打出来吧。
1. 关于安装，
每个软件的运行参数都是很多的，一定要根据自己的使用参数来启动。
prometheus
./prometheus 
--config.file=./prometheus.yml 配置文件地址
--storage.tsdb.path=/data/prometheus/prometheus/data 数据存储位置（可以本地，也可以远端）
--web.external-url="http://192.168.199.210:9090/" 这个是一个显示，针对报警邮件的按钮链接
--web.route-prefix=""  这个是链接后面的，不写就行，比较省事，
--web.listen-address="0.0.0.0:9090" 监听地址


node_exporter 没啥说道
/root/node_exporter-0.17.0.linux-amd64/node_exporter

alertmanager 启动 web.external-url web.route-prefix 和 prom一样
./alertmanager --web.external-url=http://192.168.199.210:9093/ --web.route-prefix=""

consul 启动参考 比我写的好，参考吧。 我偷懒了
https://lihaoquan.me/2018/5/31/consul-in-action.html


2. 关于过程和个人思考思路
网上图已经一堆堆了，就不在这里写了。大概文字描述一下过程

# 采集
prometheus 通过 配置文件 去 targets 采集数据 并存在本地

# 报警
prometheus 根据 rule 来匹配规则，如果符合指定条件，触发 alert
并将 报警内容和标签等信息传给 alert

关于报警规则也和其他的监控有点不一样。
配置文件的报警规则为
1. 分组
2. 延时
3. 抑制
ps：还有个静默，在页面操作，这里就不写了

1. 根据rule的lables标签进行分组
2. 如果指定时间内（延时），没变化，进行发送
3. 根据rule的lable，进行抑制，比如 服务器上有个mysql
   服务器挂了mysql必然也挂了，报警其实只需要接受服务器的就行了，这个就是抑制

了解这几个规则之后，就要考虑如何分组和如何报警了。
为了避免误报，反复报警等信息，需要多去思考如何报警


# 显示
显示没啥需要特意去配置的。直接rpm安装grafana即可。
grafana 安装后，配置数据源（prom地址）
之后去 https://grafana.com/dashboards 选择相应dashboard，通过id安装最省事。


# 信息同步
consul 配置集群，存储各种信息，各种kv信息
consul 有服务端 和 客户端。
服务端依靠命令来建立集群就行，特别省事。
客户端需要配置service信息，方便根据实际情况进行自动发现

############
举个例子：
服务器a 和 服务器b
服务器a有web应用
服务器b有redis应用

1. 服务器a和b都注册到consul，在node位置上线，并且包含service信息
2. 服务器根据自己的配置文件，分别注册 web和redis
3. prom服务器通过consul-template读取consul，读取node和service节点内容
4. 主机的consule-template模版 还有 应用的模版不同
5. 从不同的模版生成不同的配置文件
6. reload配置文件
############
根据这个方式，就可以做到无感知的同步和监控报警了。
如果真的要细化，还是比较复杂的，需要多多动脑
>>>>>>> 提供第一版，希望对大家能有帮助。逐步完善。
