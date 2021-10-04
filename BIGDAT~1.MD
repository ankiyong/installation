# Bigdata Pipeline

[ubuntu 20](https://ubuntu.com/)

계정 : big /1234



쁘티 설치 안해도 된대

ssh로 연결하면 된대

```terminal
sudo apt update
sudo apt upgrade

sudo apt install vim -y
sudo apt install openssh-server ssh-askpass -y

# -t : type(rsa) -P : 이전 암호 초기화 -f : 파일 저장 위치
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
# 생성된 public key를 저장(생성)
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

# java
wget https://corretto.aws/downloads/latest/amazon-corretto-11-x64-linux-jdk.tar.gz
tar xvzf amazon-corretto-11-x64-linux-jdk.tar.gz
ln -s amazon-corretto-11.0.12.7.1-linux-x64/ java

# path
sudo vim ~/.bashrc

# java
export JAVA_HOME=/home/big/java
export PATH=$PATH:$JAVA_HOME/bin

source ~/.bashrc

java -version
javac -version

# python3.7 (ubuntu 20 = python3.8 -> zeppelin 호환 불가)
sudo add-apt-repository ppa:deadsnakes/ppa


sudo apt install python3.7 -y

sudo apt install python3-pip -y

python3 --version
pip --version

sudo vim ~/.bashrc

# python alias
alias python=python3.7
alias pip="python3.7 -m pip"

source ~/.bashrc

python -V
pip -V

```



### hadoop

```terminal
# hadoop
# binary로 다운받기!
wget https://mirror.navercorp.com/apache/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz
tar xvzf hadoop-3.3.1.tar.gz
ln -s hadoop-3.3.1 hadoop

# path
sudo vim ~/.bashrc

# hadoop
export HADOOP_HOME=/home/big/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

# hadoop user
export HDFS_NAMENODE_USER=big
export HDFS_DATANODE_USER=big
export HDFS_SECONDARYNAMENODE_USER=big
export YARN_RESOURCEMANAGER_USER=big
export YARN_NODEMANAGER_USER=big

source ~/.bashrc

cd $HADOOP_CONF_DIR
vim hadoop-env.sh

export JAVA_HOME=/home/big/java
export HADOOP_HOME=/home/big/hadoop
export HADOOP_CONF_DIR=${AHDOOP_HOME}/etc/hadoop
# default가 tmp로 되어있어서 vm 재부팅 시 hadoop 데이터 소실 방지
export  HADOOP_PID_DIR=$HADOOP_HOME/pids

```



`vim core-site.xml`

```xml
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://localhost:9000</value>
	</property>
```



`vim hdfs-site.xml`

```xml
        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>/home/big/hadoop/namenode_dir</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>/home/big/hadoop/datanode_dir</value>
        </property>
```



`vim mapred-site.xml`

```xml
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
```



```terminal
hdfs namenode -format
hdfs datanode -format

# start-all.sh
start-dfs.sh
start-yarn.sh
# java 기반의 프로세스 확인
jps
# 총 5개 확인 : NameNode, SecondaryNameNode, DataNode, ResourceManager, NodeManager 

hdfs dfsadmin -report
# hadoop http -> localhost:9870
# yarn http -> localhost:8088

# stop-all.sh
stop-dfs.sh
stop-yarn.sh

```



### spark

```terminal
cd

# download -> 3.1.2 -> pre-built with user-provided... -> 밑에 spark-3.1.2-bin-without-hadoop 링크
wget https://mirror.navercorp.com/apache/spark/spark-3.1.2/spark-3.1.2-bin-without-hadoop.tgz
tar xvzf spark-3.1.2-bin-without-hadoop.tgz
ln -s spark-3.1.2-bin-without-hadoop spark

# path
sudo vim ~/.bashrc

# spark
export SPARK_HOME=/home/big/spark 
export PATH=$PATH:$SPARK_HOME/bin
export PATH=$PATH:$SPARK_HOME/sbin
export SPARK_DIST_CLASSPATH=$(${HADOOP_HOME}/bin/hadoop classpath)

source ~/.bashrc

cd $SPARK_HOME/conf
cp workers.template workers

cp spark-env.sh.template spark-env.sh
vim spark-env.sh

export JAVA_HOME=/home/big/java
export HADOOP_CONF_DIR=/home/big/hadoop/etc/hadoop
export YARN_CONF_DIR=/home/big/hadoop/etc/hadoop
export SPARK_DIST_CLASSPATH=$(/home/big/hadoop/bin/hadoop classpath)

export PYSPARK_PYTHON=/usr/bin/python3.7
export PYSPARK_DRIVER_PYTHON=/usr/bin/python3.7

cp spark-defaults.conf.template spark-defaults.conf
vim spark-defaults.conf

spark.master						yarn

# 실행
pyspark
# 종료
exit()

```



### zeppelin

```terminal
cd

# 가벼운 버전 (netinst)
wget https://mirror.nodesdirect.com/apache/zeppelin/zeppelin-0.10.0/zeppelin-0.10.0-bin-netinst.tgz
tar xvzf zeppelin-0.10.0-bin-netinst.tgz
ln -s zeppelin-0.10.0-bin-netinst zeppelin

sudo vim ~/.bashrc

# zeppelin
export ZEPPELIN_HOME=/home/big/zeppelin
export PATH=$PATH:$ZEPPELIN_HOME/bin

source ~/.bashrc

cd $ZEPPELIN_HOME/conf
cp zeppelin-env.sh.template zeppelin-env.sh
vim zeppelin-env.sh

export JAVA_HOME=/home/big/java
export SPARK_HOME=/home/big/spark
export HADOOP_CONF_DIR=/home/big/hadoop/etc/hadoop

cp zeppelin-site.xml.template zeppelin-site.xml

```



`vim zeppelin-site.xml`

```xml
<property>
	<name>zeppelin.server.port</name>
    <value>8082</value>
</property>
```



```terminal
# 실행
zeppelin-daemon.sh start

# browser
localhost:8082
# 오른쪽에 anonymous -> interpreter -> spark 검색 -> edit
spark.master				yarn
spark.submit.deployMode		client
# save 꼭 할 것!

# 종료
zeppelin-daemon.sh stop

```



### MySQL

```terminal
sudo apt install mysql-server -y

sudo mysql_secure_installation
# 복잡한 암호 사용 여부
n
1234
1234
# 익명 유저 삭제
y
# 외부 root접속 불가
n
# test database 삭제
y
# 권한 테이블 리로드
y

cd /etc/mysql/mysql.conf.d
sudo vim mysqld.cnf

bind-address            = 0.0.0.0
mysqlx-bind-address     = 0.0.0.0

서비스 에러시 마지막에 (2)
sudo service mysql restart
sudo systemctl restart mysql
sudo systemctl restart mysql.service
이거 셋중 하나 해봐야함

sudo mysql -u root -p
1234
show databases;
use mysql;

# sudo 없이 접속 가능
alter user 'root'@'localhost' identified with mysql_native_password by '1234';
# 어디서든 관리자 계정 접근 허용
CREATE USER 'root'@'%' IDENTIFIED BY '1234';
# 모든 명령 수행 권한
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
# 권한 변경된거 커밋
flush privileges;

CREATE TABLE test(id int, name VARCHAR(30));

INSERT INTO test VALUES(1, "hadoop");
INSERT INTO test VALUES(2, "spark");

exit

# mysql-connector-java
# mysql -> DOWNLOADS -> MySQL Community (GPL) Downloads -> Connector/J -> Ubuntu Linux -> 20.04 -> Download -> No thanks, just start my download.
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java_8.0.26-1ubuntu20.04_all.deb
sudo dpkg -i mysql-connector-java_8.0.26-1ubuntu20.04_all.deb 
# /usr/share/java/mysql-connector-java-8.0.26.jar

cd $SPARK_HOME/conf
vim spark-defaults.conf

# mysql connect
spark.jars					/usr/share/java/mysql-connector-java-8.0.26.jar

```



`pyspark`

```python
#pyspark에서 sql db,table 만들기
import mysql.connector

db_connection = mysql.connector.connect(user="me", password="me")
db_cursor = db_connection.cursor()
db_cursor.execute("CREATE DATABASE TestDB;")
db_cursor.execute("USE TestDB;")
db_cursor.execute("CREATE TABLE test(컬럼명 형식,컬럼명 형식);")

# root 계정의 mysql database에 있는 test table 사용
user="root"
password="1234"
url="jdbc:mysql://localhost:3306/TestDB"
driver="com.mysql.cj.jdbc.Driver"
dbtable="test"


test_df = spark.read.format("jdbc").option("user", user).option("password", password).option("url", url).option("driver", driver).option("dbtable", dbtable).load()
# test_df = spark.read.format("jdbc").options(user=user, password=password, url=url, driver=driver, dbtable=dbtable).load()

test_df.show()

test_insert = [(3, "mysql"), (4, "zeppelin")]
insert_df = sc.parallelize(test_insert).toDF(["id", "name"])
insert_df.show()

insert_df.write.jdbc(url, dbtable, "append", properties={"driver": driver, "user": user, "password": password})

'''
append: Append contents of this :class:DataFrame to existing data.
overwrite: Overwrite existing data. (drop -> create)
ignore: Silently ignore this operation if data already exists.	(db throw)
error (default case): Throw an exception if data already exists. (pyspark throw)
'''

```



### MongoDB

```terminal
# mongodb -> Resources -> Documentation -> Get started with MongoDB Server -> Installation Guides (젤 아래쪽 메뉴에) -> Install MongoDB Community Edition on Ubuntu

# 1 패키지 관리자 키 저장
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
# 2 mongodb 패키지 목록 저장
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
# 3 
sudo apt update
# 4 
sudo apt install -y mongodb-org
# 5
sudo systemctl start mongod
# start
mongo

db.test.insert({"id":"10", "name":"mongo"})
db.test.find()

# stop
exit

```



`vim spark-defaults.conf`

```terminal
# mongodb connect
spark.mongodb.input.uri		mongodb://localhost/test
spark.mongodb.output.uri	mongodb://localhost/test
# spark.jar.packages			org.mongodb.spark:mongo-spark-connector_2.12:3.0.1

```



*spark.jars.packages설정이 제대로 동작하지 않아서, 실행할 때 --packages 옵션으로  dependency명시*

`pyspark --packages org.mongodb.spark:mongo-spark-connector_2.12:3.0.1`

```python
test = spark.read.format("mongo").option("database", "test").option("collection", "test").load()
test.show()

insert_df = spark.createDataFrame([("11", "mongo-spark")],["id", "name"])
insert_df.write.format("mongo").option("database", "test").option("collection", "test").mode("append").save()
test.show()

'''
spark-defaults.conf 파일에다가 해당 내용 추가하면
spark.mongodb.input.database	test
spark.mongodb.input.collection	test

spark.mongodb.output.database	test
spark.mongodb.output.collection	test

.option("database", "test").option("collection", "test") 생략 가능
-> spark.read.format("mongo").load() 
'''

# https://docs.mongodb.com/spark-connector/current/python-api 참고하기!

```

