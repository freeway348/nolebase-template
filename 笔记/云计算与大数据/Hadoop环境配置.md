### 可能出现的问题及解决办法

```
本章内容仅根据尚硅谷大数据之Hadoop（入门）教程编写
```

#### 1. 三台主机之间无法连通

可能是由于在修改Hosts时将127.0.0.1对应的localhost也修改为了hadoop102/hadoop103/hadoop104，所以导致hadoop会自动优先将127.0.0.1作为主机号hadoopxxx的地址，所以无法连通，修改后（可直接删除）即可正常连接

#### 2. wordcount无法实现

1. 虽然在yarn-site.xml配置文件中，HADOOP_MAPRED_HOME和YARN_HOME均默认为hadoop安装的绝对地址，但有时候也会出现无法识别到该地址的情况，所以会导致yarn运行失败，wordcount也就无法正常使用了
2. 确认环境变量没问题后，如果还是无法成功使用wordcount，有可能是因为分配给yarn运行的虚拟内存过小，无法支持wordcount的运行，在配置文件中重新分配