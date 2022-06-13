# Second_post


# hadoop完全分布式部署

| 编号 | hostname | IP             |
| ---- | -------- | -------------- |
| 1    | master   | 192.168.18.128 |
| 2    | slave1   | 192.168.18.129 |
| 3    | slave2   | 192.168.18.130 |

## Linux环境设置

1. 设置主机名

   ```bash
   hostnamectl set-hostname [主机名]
   ```

2. 绑定主机名与IP地址

   * 编辑/etc/hosts

   ```bash
   vim /etc/hosts
   ```

3. 关闭防火墙

   * 关闭防火墙

     ```bash
     systemctl stop firewall
     ```

   * 停用防火墙

     ```
     systemctl disable firewall
     ```

     

4. **SSH免密登录**

   ```
   ssh-keygen
   ssh-copy-id
   ```

## Java安装

1. 卸载系统openjdk

   ```bash
   rpm -qa | greap jave
   rpm -e --nodeps java.....
   ```

2. 解压jdk安装包

   ```bash
   tar -zxvf /opt/softawre/jdk..... -C /usr/local/src
   ll /usr/local/src
   ```

3. 配置环境变量

   * 修改全局环境变量

     ```bash
     vi /etc/profile
     最后两行加入：export JAVA_HOME=/usr/local/src/jdk
               :export PATH=$PATH:$JAVA_HOME/bin
     保存退出
     source /etc/profile
     echo $JAVA_HOME
     ```

   * 修改用户环境变量

     ```bash
     vim /home/hadoop/.bashrc
     最后两行两行加入：export JAVA_HOME=/usr/local/src/jdk
                  :export PATH=$PATH:$JAVA_HOME/bin
     保存退出
     source /home/hadoop/.bashrc
     echo $JAVA_HOME
     ```

## hadoop安装

1. 解压安装包

   ```
   tar -zxvf ~/hadoop..... -C /usr/local/src
   改名:mv /usr/local/src/hadoop-2.7.1.tar.gz/ /usr/local/src/hadoop
   ```

2. 配置环境变量

   ```
   vi /etc/profile
   最后加入：export HADOOP_HOME=/usr/local/src/hadoop
          :export 	PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
   ```

3. 修改目录所有者

   ```
   chown -R hadoop:hadoop /usr/local/src
   检查用户所有者是否为hadoop：；ll /usr/local/src
   ```

   

4. 配置hadoop文件

   * 修改hadoop-env.sh

     ```
     cd /usr/local/src/hadoop/etc/hadoop/
     vim hadoop-env.sh
     exoprt JAVA_HOME=/usr/local/src/java
     ```

   * hdfs-site.xml

     ```
     <configuration>
      	<property>
      		<name>dfs.namenode.name.dir</name>
      		<value>file:/usr/local/src/hadoop/dfs/name</value>
      	</property>
      	<property>
      		<name>dfs.datanode.data.dir</name>
      		<value>file:/usr/local/src/hadoop/dfs/data</value>
      	</property>
      	<property>
      		<name>dfs.replication</name> 
     		<value>3</value> 
      	</property>
     </configuration>
     ```

   * yarn-site.xml

     ```
     <configuration>
      	<property>
      		<name>yarn.resourcemanager.address</name>
      		<value>master:8032</value>
      	</property>
      	<property>
      		<name>yarn.resourcemanager.scheduler.address</name>
      		<value>master:8030</value>
      	</property>
      	<property>
      		<name>yarn.resourcemanager.resource-tracker.address</name>
      		<value>master:8031</value>
      	</property>
      	<property>
      		<name>yarn.resourcemanager.admin.address</name>
      		<value>master:8033</value>
      	</property>
      	<property>
      		<name>yarn.resourcemanager.webapp.address</name>
      		<value>master:8088</value>
      	</property>
      	<property>
      		<name>yarn.nodemanager.aux-services</name>
      		<value>mapreduce_shuffle</value>
      	</property>
      	<property>
      		<name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
      		<value>org.apache.hadoop.mapred.ShuffleHandler</value>
      	</property>
     </configuration>
     ```

   * mapred-site.xml

     ```
     <configuration>
      	<property>
      		<name>mapreduce.framework.name</name>
      		<value>yarn</value>
      	</property>
      	<property>
      		<name>mapreduce.jobhistory.address</name>
      		<value>master:10020</value>
      	</property>
      	<property>
      		<name>mapreduce.jobhistory.webapp.address</name>
      		<value>master:19888</value>
      	</property>
     </configuration>
     ```

   * core-site.xml

     ```
     <configuration>
      	<property>
      		<name>fs.defaultFS</name>
      		<value>hdfs://192.168.150.81:9000</value>
      	</property>
      	<property>
      		<name>io.file.buffer.size</name>
      		<value>131072</value>
      	</property>
      	<property>
      		<name>hadoop.tmp.dir</name>
      		<value>file:/usr/local/src/hadoop/tmp</value>
      	</property>
     </configuration>
     ```

   * slaves

     ```
     slave1
     slave2
     ```


- ​          启动完之后 slave1 slave2 datanode 没开启 

  ​        

  ```
  1.检查JPShadoop是否节点都正常启动
  2.删除/usr/loacl/src/hadoop/dfs/data/current/VERSION文件后重启
  ```

  

# hive

```
出问题打不开关闭安全模式   hdfs dfsadmine -safemode leave
```

## zookeeper

```
出问题后
1.确认文件
2.删除/usr/local/src/zookeeper/data/  后 echo (数字) > /usr/local/src/zookeeper/data/myid
  再 source /etc/profile 
3.确认端口是否占用（2181）netstat -apn | grep 2181
  有占用就删除 kill -9 (进程号)
```


