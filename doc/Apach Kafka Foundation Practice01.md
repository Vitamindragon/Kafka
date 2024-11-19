> 01.Single Configuration[Practice01]
- DockerFile
- server.properties
- zookeeper.properties

> 02.Execution[Practice01]
~~~bash
#Docker Build
docker build --tag practice01 .

#Docker Container Exec
sudo docker run -it -d --privileged=true --name "practice01" practice01 /sbin/init

#Docker 실행
docker exec -it practice01 /bin/bash           

#실행
cd /app/kafka/bin ./zookeeper-server-start.sh /app/kafka/config/zookeeper.properties 

cd /app/kafka/bin ./kafka-server-start.sh /app/kafka/config/server.properties 


~~~

>03.Result[Practice01]

- **zookeeper 실행**
	- ![[Pasted image 20241119002720.png]]

- **kafka 실행**
	- ![[Pasted image 20241119002733.png]]

**Producer**
- ![[Pasted image 20241119100449.png]]

**Consumer**
- ![[Pasted image 20241119100528.png]]
