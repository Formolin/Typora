# ElasticSearch

![千锋Java教程：2_Restfull风格和Curl命令_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g59q7ywaguj31l60l2k4t.jpg)

![千锋Java教程：2_Restfull风格和Curl命令_mp4](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g59qa2m8haj30ww0myteq.jpg)

## docker-compose安装

```sh
 cd /usr/local/docker

 vi docker-compose.yml
 

```

```sh
version: '2'
services:
  elastic:
    image: bachue/elasticsearch-ik:6.2.4
    container_name: elastic
    environment:
      - TZ='Asia/Shanghai'
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    mem_limit: 1g
    volumes:
      - ./elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
      - ./data:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
      - 9300:9300
  elasticsearch-head:
      image: wallbase/elasticsearch-head:6-alpine
      container_name: elasticsearch-head
      environment:
        TZ: 'Asia/Shanghai'
      ports:
        - '9100:9100'
  kibana:
     image: docker.elastic.co/kibana/kibana:6.4.0 
     environment: 
      SERVER_NAME: kibana 
      ELASTICSEARCH_URL: http://192.168.1.131:9200 
     ports: 
      - "5600:5600" 
```

```sh
version: '2.2' # Version2支持更多的指令 
services: # 包含需要操作的容器 
elasticsearch: # 容器的名称 
	image: docker.elastic.co/elasticsearch/elasticsearch:6.4.0 # 指定基于哪个镜像 
  environment: 
    - cluster.name=esCluster # 集群名字,没有就不管 
    - "ES_JAVA_OPTS=-Xms512m -Xmx512m" # JVM大小 
  ports: 
    - 9200:9200 # 指定映射的端口 
kibana: 
  image: docker.elastic.co/kibana/kibana:6.4.0 
  environment: 
    SERVER_NAME: kibana 
    ELASTICSEARCH_URL: http://ES的IP:9200 
  ports: 
    - "5601:5601" 
elasticsearch-head:
  image: wallbase/elasticsearch-head:6-alpine
  container_name: elasticsearch-head
  environment:
  	TZ: 'Asia/Shanghai'
  ports:
  - '9100:9100'

```

```sh
version: '2.2'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.2.0
    container_name: elasticsearch
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - esdata1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
 kibana: 
  image: docker.elastic.co/kibana/kibana:7.2.0
  environment: 
    SERVER_NAME: kibana 
    ELASTICSEARCH_URL: http://192.168.1.131:9200 
  ports: 
    - "5601:5601"  

volumes:
  esdata1:
    driver: local

networks:
  esnet
```



