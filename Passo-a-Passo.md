Baseado nos artigos:

[Como Instalar O Hadoop No Ubuntu 18.04 Ou 20.04](https://elisiogama86.medium.com/como-instalar-o-hadoop-no-ubuntu-18-04-ou-20-04-c1574d5da6d7)
[Instalando Hadoop e Mahout no Ubuntu 16.04 e 18.04](https://medium.com/@ademarcfreitas/instalando-hadoop-e-mahout-no-ubuntu-16-04-e-18-04-b0c640bf4bbd)

# Pré-requisitos

[VMWare Workstation Player 17](https://download3.vmware.com/software/WKST-PLAYER-1702/VMware-player-full-17.0.2-21581411.exe) - Apenas se for usar máquina virtual
[Ubuntu 20](https://releases.ubuntu.com/focal/ubuntu-20.04.6-desktop-amd64.iso)
Acesso a uma janela de terminal / linha de comando
Privilégios de sudo ou root

OBS: Nesse repositorio tem todos os arquivos utilizados, porém priorize as linhas de comando.

# Instale OpenJDK no Ubuntu

```
sudo apt update
sudo apt install openjdk-8-jdk -y
java -version; javac -version
```

# Instale OpenSSH no Ubuntu

```
sudo apt install openssh-server openssh-client -y
```

# Criar usuário Hadoop

```
sudo adduser hadoop
su - hadoop
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```

# Conectar no ssh local

```
ssh localhost
```

# Baixar e instalar o Hadoop

```
wget https://downloads.apache.org/hadoop/common/hadoop-3.2.4/hadoop-3.2.4.tar.gz
tar xzf hadoop-3.2.4.tar.gz
```

# Definindo as variaveis de ambiente

```
nano ~/.bashrc
```

# Insira no final do arquivo:

```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/home/hadoop/hadoop-3.2.4
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```

# Atualizando bashrc

```
source ~/.bashrc
```

# Configurando o hadoop-env.sh

```
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

## Procure por:

```
export JAVA_HOME=
```

## Altere para:

```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

# Editar arquivo core-site.xml

```
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```

## Adicione no final (atencao com o <configuration>)

```
<configuration>
 <property>
  <name>hadoop.tmp.dir</name>
  <value>/home/hadoop/tmpdata</value>
 </property>
 <property>
  <name>fs.default.name</name>
  <value>hdfs://127.0.0.1:9000</value>
 </property>
</configuration>
```

# Editar arquivo hdfs-site.xml

```
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```

## Adicione no final (atencao com o <configuration>)

```
<configuration>
 <property>
  <name>dfs.data.dir</name>
  <value>/home/hadoop/dfsdata/namenode</value>
 </property>
 <property>
  <name>dfs.data.dir</name>
  <value>/home/hadoop/dfsdata/datanode</value>
 </property>
 <property>
  <name>dfs.replication</name>
  <value>1</value>
 </property>
</configuration>
```

# Editar arquivo mapred-site.xml

```
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

## Adicione no final (atencao com o <configuration>)

```
<configuration>
 <property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
 </property>
</configuration>
```

# Editar o arquivo yarn-site.xml

```
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```

## Adicione no final (atencao com o <configuration>)

```
<configuration>
 <property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
 </property>
 <property>
  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
 </property>
 <property>
  <name>yarn.resourcemanager.hostname</name>
  <value>127.0.0.1</value>
 </property>
 <property>
  <name>yarn.acl.enable</name>
  <value>0</value>
 </property>
 <property>
  <name>yarn.nodemanager.env-whitelist</name>
  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
 </property>
</configuration>
```

# Baixando o Reuters C50

```
cd ~
wget https://archive.ics.uci.edu/static/public/217/reuter+50+50.zip
unzip reuter+50+50.zip
mkdir C50
mv C50t* ./C50
```

# Criando diretorios / Permissões

```
sudo mkdir /home/hadoop/tmpdata
sudo mkdir /home/hadoop/dfsdata/
sudo mkdir /home/hadoop/dfsdata/namenode
sudo mkdir /home/hadoop/dfsdata/datanode
sudo chmod 777 -R /home/hadoop/tmpdata/
sudo chmod 777 -R /home/hadoop/dfsdata/
sudo chmod 777 -R /home/hadoop/dfsdata/*
sudo chown hadoop:hadoop -R /home/hadoop/tmpdata/
sudo chown hadoop:hadoop -R /home/hadoop/dfsdata/
sudo chown hadoop:hadoop -R /home/hadoop/dfsdata/*
sudo chown hadoop:hadoop -R /home/hadoop/C50/*
```

# Formato HDFS NameNode

```
hdfs namenode -format
```

# Iniciando Serviços

```
cd ~/hadoop-3.2.4/sbin/
./start-dfs.sh
./start-yarn.sh
```

## Execute: jps

Veja se lista os modulos:

```
$ jps
72805 Jps
22308 DataNode
22680 ResourceManager
22825 NodeManager
22138 NameNode
22509 SecondaryNameNode
```

Se sim, abra o navegador e veja se carrega a pagina: http://localhost:9870

# Instalando o mahout

```
cd ~
wget https://archive.apache.org/dist/mahout/0.12.2/apache-mahout-distribution-0.12.2-src.tar.gz
tar -xvf apache-mahout-distribution-0.12.2-src.tar.gz
sudo mv apache-mahout-distribution-0.12.2 /usr/local/mahout
```

## Adicionar as variaveis de ambiente

```
nano .bashrc
```

Adicione:

```
export MAHOUT_HOME="/usr/local/mahout"
```

Altere o PATH para (OBS: Essa deve ser a ultima linha do arquivo)

```
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin:$MAHOUT_HOME/bin
```

## Salve e execute

```
source .bashrc
```

## Instalando utilitarios

```
sudo apt-get install maven
sudo apt-get install curl
cd $MAHOUT_HOME
mvn -DskipTests clean install
```

## OBS: o ultimo comando acima vai demorar bastante para finalizar

# Executando o codigo proposto no material da cruzeiro do sul.

## OBS: execute linha a linha.

```
cd ~
hadoop fs -copyFromLocal C50/ /
./mahout seqdirectory -i /C50/C50train -o /seqreuters -xm sequential
./mahout seq2sparse -i /seqreuters -o /train-sparse
./mahout kmeans -i /train-sparse/tfidf-vectors/ -c /kmeans-train-clusters -o /train-clusters-final -dm org.apache.mahout.common.distance.EuclideanDistanceMeasure -x 10 -k 10 -ow
./mahout clusterdump -d /train-sparse/dictionary.file-0 -dt sequencefile -i /train-clusters-final/clusters-10-final -n 10 -b 100 -o ~/saida_clusters.txt -p /train-clusters-final/clustered-points
```


No final será gerado um arquivo "saida_clusters.txt" com o resultado.
