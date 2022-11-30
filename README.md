## 简单搭建一个可视化mysql监控

当数据访问量比较大时我们往往会对数据库进行调优，但是在调优之前如果我们知道数据库的访问情况，那么就可以更好的对其进行处理。本项目就是简单搭建一个可视化的监控，以便我们更好的对数据库进行监控。

主要用到的工具是**普罗米修斯**+**mysql-exporter**+**grafana**，可以在官网进行下载，也可以使用文章末尾的下载链接。搭建步骤如下：

#### 1.部署mysql-exporter

首先解压缩下载的压缩包

![image-20221130113512853](./img/1.png)

然后创建一个my.cnf配置文件，表示要采集的数据库信息，其配置如下：

```bash
exporter配置
[client]
user=root
password=xxxx
host=xxxx
port=3306
```

![image-20221130154124832](./img/2.png)

通过以下命令启动exporter：

```bash
./mysqld_exporter --config.my-cnf=./my.cnf
```

![image-20221130154629735](./img/3.png)

现在采集数据库信息的exporter已经启动了，运行在`9104`端口

![image-20221130155148764](./img/4.png)

> 如果访问不了可能是因为你的服务器没有打开9104端口，需要在安全组开放一下

其中的metrics点进来就是我们采集到的数据库信息了

![image-20221130155621065](./img/5.png)

现在采集到的数据指标只是文字形势的，我们要将其转为仪表盘那样可视化的，这就需要用到**普罗米修斯**。

#### 2.部署prometheus

先将其解压：

![image-20221130160138996](./img/6.png)

接着直接启动即可：

```bash
./prometheus
```

![image-20221130160950023](./img/7.png)

普罗米修斯运行在`9090`端口，访问进来的页面如下：

![image-20221130160852776](./img/8.png)

通过`Status`->`Targets`，就可以看到其采集的信息

![image-20221130161230120](./img/9.png)

这里看到它采集到的信息只有prometheus自己的，没有mysql的，是因为我们还没有对其进行配置，通过`prometheus.yml`进行配置：

```bash
vim prometheus.yml
```

![image-20221130162121623](./img/10.png)

修改完之后重新启动一下prometheus，使配置生效，再访问发现mysql也已经采集到了

![image-20221130162336768](./img/11.png)

#### 3.集成grafana

解压后进入bin目录下，启动grafana

```bash
./grafana-server
```

访问`3000`端口，默认的账户名和密码都是`admin`（登录后它会让你改一次密码）

![image-20221130163113427](./img/12.png)

登录进来后，先点击配置进行配置数据源

![image-20221130163518958](./img/13.png)

![image-20221130163549501](./img/14.png)

数据源选择普罗米修斯

![image-20221130163612498](./img/15.png)

配置一下url

![image-20221130163809124](./img/16.png)

点击save&test

![image-20221130163830486](./img/17.png)

添加成功后，点击Dashboards

![image-20221130163925057](./img/18.png)

点击Import导入仪表盘，仪表盘可以从官网去找，也可以自己定制上传

![image-20221130164039481](./img/19.png)

仪表盘url如下：

```bash
https://grafana.com/grafana/dashboards/7362-mysql-overview/
```

点击load，选择数据源普罗米修斯，然后import

![image-20221130164331656](./img/20.png)

导入成功后就是这个界面：

![image-20221130164442351](./img/21.png)

至此一个简单的数据库监控平台就算搭建完成了。



普罗米修斯+mysql-exporter+grafana的windows版本和linux版本都在下面链接中：

链接：https://pan.baidu.com/s/1LexcROGLKmtUKzQ1YHDrog?pwd=0504 
提取码：0504
