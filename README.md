# Big_Data
# Состав команды: Нечайкин Владислав, Пономарев Михаил, Яковлев Семён
# Домашнее задание 1
## Развертывание кластера hadoop

### 1. Делаем на jumpnode

#####  Подключение к jump node 
ssh team@ip 
##### Создание пользователя hadoop 
sudo adduser hadoop 
##### Смена пользователя на hadoop
sudo -i -u hadoop
#####  Скачивание hadoop
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz
##### Генерация .ssh ключа 
ssh-keygen
##### Сгенерированный ключ лежит здесь, его нужно сохранить
cat ~/.ssh/id_ed25519.pub
##### Выход из пользвателя hadoop
exit
exit
##### Открытие файла hosts
sudo vim /etc/hosts
##### Вставляем ip адреса наших нод и именуем их, все остальное удаляем
ins
```
192.168.1.118	team-29-jn
192.168.1.119	team-29-nn
192.168.1.120	team-29-dn-00
192.168.1.121	team-29-dn-01
```
esc
:wq
##### Проверим, что ноды пингуются
ping team-29-jn 
ping team-29-nn 
ping team-29-dn-00 
ping team-29-dn-01

### 2. Делаем на namenode

#####  Подключение к namenode
ssh team@team-29-nn
##### Открытие файла hosts
sudo vim /etc/hosts
##### Вставляем ip адреса наших нод и именуем их, все остальное удаляем
```
ins

192.168.1.118	team-29-jn
192.168.1.119	team-29-nn
192.168.1.120	team-29-dn-00
192.168.1.121	team-29-dn-01

esc
:wq
```
##### Создание пользователя hadoop 
sudo adduser hadoop 
##### Смена пользователя на hadoop
sudo -i -u hadoop
##### Генерация .ssh ключа 
ssh-keygen
##### Сгенерированный ключ лежит здесь, его нужно сохранить
cat ~/.ssh/id_ed25519.pub
##### Выходим с hadoop
exit
##### Выходим с ноды
exit

### 3. Делаем на datanode 00

#####  Подключение к datanode 00
ssh team@team-29-dn-00
##### Открытие файла hosts
sudo vim /etc/hosts
##### Вставляем ip адреса наших нод и именуем их, все остальное удаляем
```
ins

192.168.1.118	team-29-jn
192.168.1.119	team-29-nn
192.168.1.120	team-29-dn-00
192.168.1.121	team-29-dn-01

esc
:wq
```
##### Создание пользователя hadoop 
sudo adduser hadoop 
##### Смена пользователя на hadoop
sudo -i -u hadoop
##### Генерация .ssh ключа 
ssh-keygen
##### Сгенерированный ключ лежит здесь, его нужно сохранить
cat ~/.ssh/id_ed25519.pub
##### Выходим с hadoop
exit
##### Выходим с ноды
exit

### 4. Делаем на datanode 01

#####  Подключение к datanode 01
ssh team@team-29-dn-01
##### Открытие файла hosts
sudo vim /etc/hosts
##### Вставляем ip адреса наших нод и именуем их, все остальное удаляем
```
ins

192.168.1.118	team-29-jn
192.168.1.119	team-29-nn
192.168.1.120	team-29-dn-00
192.168.1.121	team-29-dn-01

esc
:wq
```
##### Создание пользователя hadoop 
sudo adduser hadoop 
##### Смена пользователя на hadoop
sudo -i -u hadoop
##### Генерация .ssh ключа 
ssh-keygen
##### Сгенерированный ключ лежит здесь, его нужно сохранить
cat ~/.ssh/id_ed25519.pub
##### Выходим с hadoop
exit
##### Выходим с ноды
exit

### 5. Делаем на jumpnode
##### Смена пользователя на hadoop
sudo -i -u hadoop
##### Создаем файл для хранения ключей и вставляем ключи, сохраненные на шагах раньше
vim .ssh/authorized_keys

##### Копируем файл с ключами и hadoop на все ноды
scp .ssh/authorized_keys team-29-nn:/home/hadoop/.ssh/
scp .ssh/authorized_keys team-29-dn-00:/home/hadoop/.ssh/
scp .ssh/authorized_keys team-29-dn-01:/home/hadoop/.ssh/

##### Копируем архив с hadoop на все ноды
scp hadoop-3.4.0.tar.gz team-29-nn:/home/hadoop
scp hadoop-3.4.0.tar.gz team-29-dn-00:/home/hadoop
scp hadoop-3.4.0.tar.gz team-29-dn-01:/home/hadoop

### 6. Делаем на namenode, datanode 00, datanode 01

##### Подключение к namenode
ssh team-29-nn 
##### Распакуем переданный архив
tar -xvzf hadoop-3.4.0.tar.gz 
exit

##### Подключение к datanode 00
ssh team-29-dn-00
##### Распакуем переданный архив
tar -xvzf hadoop-3.4.0.tar.gz 
exit

##### Подключение к datanode 01
ssh team-29-dn-01
##### Распакуем переданный архив
tar -xvzf hadoop-3.4.0.tar.gz 
exit

## Настройка hadoop
### 7. Делаем на namenode

ssh team-29-nn

##### Сохраняем путь до джавы
readlink -f /usr/bin/java

##### Открываем файл profile
vim ~/.profile
##### Добавляем в файл 3 переменные

##### где распакован hadoop
export HADOOP_HOME=/home/hadoop/hadoop-3.4.0
##### путь до джавы
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
##### путь для выполнения исполняемых файлов hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

##### Перезагрузим окружение
source ~/.profile
##### Проверим, что все ок
hadoop version

##### Копируем файл profile на data nodes
scp ~/.profile team-29-dn-00:/home/hadoop
scp ~/.profile team-29-dn-01:/home/hadoop

##### Переходим в папку hadoop
cd hadoop-3.4.0/etc/hadoop
##### Редактируем файл и вставляем переменную окружения 
vim hadoop-env.sh 
```
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```
esc
:wq

##### Копируем hadoop-env.sh на data nodes

scp hadoop-env.sh team-29-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop
scp hadoop-env.sh team-29-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop

##### Редактируем файл сore-site.xml
vim core-site.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://team-29-nn:9000</value>
    </property>
</configuration>
```

##### Редактируем файл 
vim hdfs-site.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>3</value>
    </property>
</configuration>
```

esc
:wq
##### Редактируем файл vim workers
vim workers
##### поменяем localhost на имена нод
```
team-29-nn
team-29-dn-00
team-29-dn-01
```
esc
:wq

##### Копируем отредактированные файлы на data nodes
scp core-site.xml team-29-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop
scp core-site.xml team-29-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop

scp hdfs-site.xml team-29-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop
scp hdfs-site.xml team-29-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop

scp workers team-29-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop
scp workers team-29-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop

## Запуск кластера Hadoop\
### 8. Переходим в папку hadoop-3.4.0
cd ~/hadoop-3.4.0
##### Форматируем файловую систему
bin/hdfs namenode -format
##### Запускаем хадуп
sbin/start-dfs.sh
##### Для проверки
jps

### 9. Настройка nginx
##### Возвращаемся на jump node
ssh team-29-jn
##### Копируем конфиг
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/nn
##### Открываем конфиг
sudo vim /etc/nginx/sites-available/nn
##### Вставляем в файл
```
server {
            listen 9870 default_server;

            root /var/www/html;

            index index.html index.htm index.nginx-debian.html;

            server_name _;

            location / {

                proxy_pass http://team-29-nn:9870;
                        }
            }
```
##### Создаем ссылку
sudo ln -s /etc/nginx/sites-available/nn /etc/nginx/sites-enabled/nn
##### перезапуск nginx
sudo systemctl reload nginx
##### Откроем в браузере для проверки
`http://ip:9870/`


# Домашнее задание 2
## Развертывание YARN
### 1. Редактируем конфиги 
##### Заходим на jumpnode
ssh team@ip
##### Меняем юзера на hadoop
sudo -i -u hadoop
##### Переходим на namenode
ssh team-29-nn
##### Переходим в папку hadoop
cd hadoop-3.4.0/etc/hadoop
##### Меняем конфигурацию файла mapred-site.xml
```
    <?xml version="1.0"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

    <configuration>
            <property>
                    <name>mapreduce.framework.name</name>
                    <value>yarn</value>
            </property>
            <property>
                    <name>mapreduce.application.classpath</name>
                    <value>$HADOPP_HOME/share/hadoop/mapreduce/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*</value>
            </property>
    </configuration>
```
##### Меняем конфигурацию файла yarn-site.xml
```
 <?xml version="1.0"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
    <configuration>
            <property>
                    <name>yarn.nodemanager.aux-services</name>
                    <value>mapreduce_shuffle</value>
            </property>
            <property>
                    <name>yarn.nodemanager.env-whitelist</name>
                    <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE_HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
            </property>

    </configuration>
```
##### Копируем получившиеся конфиги на data nodes с помощью

scp mapred-site.xml team-29-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop
scp mapred-site.xml team-29-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop

scp yarn-site.xml team-29-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop
scp yarn-site.xml team-29-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop

##### Переходим в папку hadoop
cd /home/hadoop/hadoop-3.4.0/
##### Запускаем YARN
./sbin/start-yarn.sh
##### Запускаем historyserver
mapred --daemon start historyserver

### 2. Поднимаем веб-интерфейс для YARN и historyserver
##### Заходим на jumpnode
ssh team@ip
##### Копируем файлы 
sudo cp /etc/nginx/sites-available/nn /etc/nginx/sites-available/ya

sudo cp /etc/nginx/sites-available/nn /etc/nginx/sites-available/dh

##### Создаем ссылки
sudo ln -s /etc/nginx/sites-available/dh /etc/nginx/sites-enabled/dh

sudo ln -s /etc/nginx/sites-available/ya /etc/nginx/sites-enabled/ya

##### Перезапускаем nginx
sudo systemctl reload nginx

##### Проверяем по ссылкам
##### YARN
http://ip:8088/
##### historyserver
http://ip:19888/

# Домашнее задание 3
### 1. Делаем на jump-node

##### Подключение к jump-node 
ssh team@ip
##### Сменим активного пользователя на hadoop 
sudo -i -u hadoop
##### Скачаем дистрибутив hive 
wget https://dlcdn.apache.org/hive/hive-4.0.1/apache-hive-4.0.1-bin.tar.gz
##### Распакуем его 
tar -xzvf apache-hive-4.0.1-bin.tar.gz
##### Распакуем его 
tar -xzvf hadoop-3.4.0.tar.gz
##### Перейдем в 
cd apache-hive-4.0.1-bin/lib/
##### Скачаем jdbc
wget https://jdbc.postgresql.org/download/postgresql-42.7.4.jar
##### Создадим файл
cd ../conf/
vim hive-site.xml
с таким содержимым
```
<configuration>
    <property>
        <name>hive.server2.authentication</name>
        <value>NONE</value>
    </property>
    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
    </property>
    <property>
        <name>hive.server2.thrift.port</name>
        <value>5449</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:postgresql://team-29-nn:5432/metastore</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>org.postgresql.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hive</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value><postgre password></value>
    </property>
</configuration>
```

##### Изменим profile и активируем
vim ~/.profile
```
export HIVE_HOME=/home/hadoop/apache-hive-4.0.1-bin
export HIVE_CONF_DIR=$HIVE_HOME/conf
export HIVE_AUX_JARS_PATH=$HIVE_HOME/lib/*
export PATH=$HIVE_HOME/bin:$PATH
```
source ~/.profile

### 2. Делаем на name-node под team, так как нужны права sudo
##### Установим postgres
sudo apt install postgresql
##### Создание пользователя postgres 
sudo adduser postgres 
##### Перейдем на пользователя postgres
sudo -i -u postgres
##### Подключимся к консоли
psql
##### Создадим бд, пользователя, добавим права и сделаем его владельцем
CREATE DATABASE metastore;
CREATE USER hive WITH PASSWORD '<password>';
GRANT ALL PRIVILEGES ON DATABASE "metastore" TO hive;
ALTER DATABASE metastore OWNER TO hive;
##### Выходим из консоли и из под postgres
\q
exit
##### Изменим postgresql.conf
sudo vim /etc/postgresql/16/main/postgresql.conf
##### добавим наш адрес в просулшиваемый
listen_addresses = 'team-29-nn'
##### Изменим postgresql.conf
sudo vim /etc/postgresql/16/main/pg_hba.conf 
удалим строку с 
host    all             all             127.0.0.1/32       scram-sha-256
Добавим после IPv4 local connections 
host    metastore       hive            ip/32              password     

##### Перезапуск постгрес
sudo systemctl restart postgresql

##### Вернемся на jn
exit
##### Установка клиента постгреса из под team
sudo apt install postgresql-client-16

#### Переходим на namenode из-под hadoop
ssh hadoop@team-29-nn

##### Создадим папку 
hdfs dfs -mkdir /tmp
##### Создадим папку 
hdfs dfs -mkdir -p /user/hive/warehouse
##### Выдадим права на папку 
hdfs dfs -chmod g+w /tmp
##### Выдадим права на папку, в которой будут храниться данные 
hdfs dfs -chmod g+w /user/hive/warehouse

exit

#### Установка hadoop на jn
ssh hadoop@team-29-jn

#### Разархивируем hadoop
tar -xvzf hadoop-3.4.0.tar.gz

##### Открываем файл profile
vim ~/.profile
##### Добавляем в файл 3 переменные

##### где распакован hadoop
export HADOOP_HOME=/home/hadoop/hadoop-3.4.0
##### путь до джавы
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
##### путь для выполнения исполняемых файлов hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
##### Перезагрузим окружение
source ~/.profile
##### Проверим, что все ок
hadoop version

##### Переходим в папку hadoop
cd hadoop-3.4.0/etc/hadoop

##### Редактируем файл сore-site.xml
vim core-site.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://team-29-nn:9000</value>
    </property>
</configuration>
```

##### Редактируем файл hdfs-site.xml
vim hdfs-site.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>3</value>
    </property>
</configuration>
```

esc
:wq

### 3. Делаем на jump-node под hadoop
##### Перейдем в папку 
cd  ../apache-hive-4.0.1-bin/
##### Инициализировать внутреннюю БД HIVE
bin/schematool -dbType postgres -initSchema
##### Выполним команду для использования в локальном окружении 
hive --hiveconf hive.server2.enable.doAs=false --hiveconf hive.security.authorization.enabled=false --service hiveserver2 1>> /tmp/hs2.log 2>> /tmp/hs2.log &

##### Подключимся к Hive
beeline -u jdbc:hive2://team-29-jn:5449
##### Заберем .csv файл
wget https://raw.githubusercontent.com/Semyon-Yakovlev/Big_Data/refs/heads/main/diamonds.csv

hdfs dfs -mkdir /user/data
hdfs dfs -copyFromLocal /home/hadoop/diamonds.csv /user/data/
##### Создадим датабазу и таблицу, выполнив последовательно:
CREATE DATABASE test;
CREATE TABLE IF NOT EXISTS test.diamonds (
  id int,
  carat float,
  cut string,
  color string,
  clarity string,
  depth float,
  table float,
  price int,
  x float,
  y float,
  z float
) COMMENT 'diamonds' 
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
STORED AS textfile
tblproperties("skip.header.line.count"="1");
LOAD DATA INPATH '/user/data/diamonds.csv' INTO TABLE test.diamonds;
##### Далее полученную таблицу трансформируем в партиционированную:
CREATE TABLE test.diamonds_partitioned
PARTITIONED BY (cut)
AS SELECT * FROM test.diamonds;

### Выход из safemode 
##### Можно настроить параметры в hdfs-site.xml для автоматического выхода из safemode:

vim hdfs-site.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>3</value>
    </property>
    <property>
        <name>dfs.namenode.safemode.threshold-pct</name>
        <value>0.9</value><!-- 90% блоков должны быть доступны -->
    </property>
    <property>
        <name>dfs.safemode.min.datanodes</name>
        <value>3</value> <!-- Минимум 3 DataNode должны быть активны -->
    </property>
</configuration>
```
##### После изменения настроек потребуется перезапустить NameNode, в некоторых случаях достаточно просто перезапустить NameNode
