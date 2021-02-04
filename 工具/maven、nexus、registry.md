# Maven

- mvn clean package依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)等７个阶段。
- mvn clean install依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install等8个阶段。
- mvn clean deploy依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install、deploy等９个阶段。

### 　　 由上面的分析可知主要区别如下

- package命令完成了项目编译、单元测试、打包功能，但没有把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库

- install命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库，但没有布署到远程maven私服仓库

- deploy命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库　

# Nexus

## 基于docker安装

- docker pull sonatype/nexus3
- 进入usr/local/docker
- 创建nexus文件夹
- 进入文件夹

- 我们使用docker安装和运行nexus，vi docker-compose.yml

  ```yml
  version: '3.1'
  services:
   nexus:
    restart: always
    image: sonatype/nexus3
    container_name: nexus
    ports:
     - 8081:8081
    volumes:
     - /usr/local/docker/nexus/data:/nexus-data
  ```

- mkdir data
- chmod 777 data/    给data目录权限
- docker-compose up -d(docker-compose down)

- 第一访问连接失败，等一会在刷新，需要点时间
- 192.168.83.200:8081
- 默认用户名密码
- admin
- admin123
- ![Repositories_-_Nexus_Repository_Manager](https://ws4.sinaimg.cn/large/006tNc79ly1g2yll3sotqj321b0u0119.jpg)

- **maven修改本地仓库地址：**

  还是在这个setting.xml文件,搜索localRepository:

  ```sh
  在下面加入一行<localRepository>D:\down\maven-repository\repository</localRepository>，这里是我自己的maven本地仓库地址，改成自己设置的maven本地仓库地址
  
  
  然后将setting.xml（maven安装的位置D:\maven\apache-maven-3.3.9\conf\setting.xml），复制一份到你设置的maven本地仓库地址下，
  
  这里的地址是D:\down\maven-repository\repository，配置完成了打开intellij idea,打开file->setting,搜索maven:
  ```

  ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g2ym387t16j30yz0op42g.jpg)

- 注意勾选上always update snapshots，始终使用最新的快照

- 在settings.xml中的servers节点下，添加下面配置

  ```xml
  <server>
  	<id>nexus-releases</id>
    <username>admin</username>
    <password>admin123</password>
  </server>
  
  <server>
  	<id>nexus-snapshots</id>
    <username>admin</username>
    <password>admin123</password>
  </server>
  ```

- 配置自动化部署-项目与私服关联上

  在pom中添加

  ```xml
  <distributionManagement>
  	<repository>
    	<id>nexus-releases</id>
      <name>Nexus Release Repository</name>
      <url>http://192.168.0.100:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
    	<id>nexus-releases</id>
      <name>Nexus snapshots Repository</name>
      <url>http://192.168.0.100:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
  </distributionManagement>
  ```

- ![Repositories_-_Nexus_Repository_Manager](https://ws1.sinaimg.cn/large/006tNc79ly1g2ymjq3ik0j31c20mun18.jpg)

- 发布

  ```sh
  mvn deploy -Dmaven.test.skip=true
  ```

  

# Registry(docker镜像私服)

- usr/local/docker下创建registry文件夹

- vi docker-compose.yml

  ```yml
  version: '3.1'
  services:
   registry:
    restart: always
    image: registry
    container_name: registry
    ports:
     - 5000:5000
    volumes:
     - /usr/local/docker/registry/data:/var/lib/registry
  ```

- docker-compose up -d

- 192.168.83.200:5000/v2

- 没有镜像，所以是空

- docker pull是从官服下载

- 我们上面的配置是服务端，需要在客户端上配置一下

- cd /etc/docker

- vi daemon.json

  ```sh
  {
  	"registry-mirrors":[
  		"https://registry.docker-cn.com"
  	],
  	"insecure-registries":[
  		"192.168.83.200:5000"
  	]
  }
  ```

- 重启docker服务
- systemctl restart docker
- 检查客户端配置是否生效
- docker info![liujiang_—_root_bogon__etc_docker_—_ssh_root_192_168_83_200_—_141×24](https://ws2.sinaimg.cn/large/006tNc79ly1g2znaibhzyj30yw0cctcu.jpg)

- 测试上传下载镜像
- docker pull tomcat
- docker tag tomcat 192.168.83.200:5000/tomcat   标记一下本地的tomcat镜像取私服  并取名为tomcat
- docker push 192.168.83.200:5000/tomcat

```sh
访问
http://192.168.83.200:5000/v2/_catalog
```

### 安装图形化管理工具操作仓库

- 修改之前的registry的docker-compose.yml

- docker-compose.yml

  ```yml
  version: '3.1'
  services:
   frontend:
    image: konradkleine/docker-registry-frontend:v2
    #image: docker pull konradkleine/docker-registry-frontend
    container_name: registry
    ports:
     - 8080:80
    volumes:
     - ./certs/frontend.crt:/etc/apache2/server.crt:ro
     - ./certs/frontend.key:/etc/apache2/server.key:ro
    environment:
     - ENV_DOCKER_REGISTRY_HOST=192.168.83.200
     - ENV_DOCKER_REGISTRY_PORT=5000
  ```

  完整

  ```yml
  version: '3.1'
  services:
   registry:
    restart: always
    image: registry
    container_name: registry
    ports:
     - 5000:5000
    volumes:
     - /usr/local/docker/registry/data:/var/lib/registry
  
  
   frontend:
    image: konradkleine/docker-registry-frontend:v2
    ports:
     - 5001:80
     #这里映射额5001 是暴露给外面的访问端口
    volumes:
     - ./certs/frontend.crt:/etc/apache2/server.crt:ro
     - ./certs/frontend.key:/etc/apache2/server.key:ro
    environment:
     - ENV_DOCKER_REGISTRY_HOST=192.168.83.200
     - ENV_DOCKER_REGISTRY_PORT=5000
  ```

- 启动 docker-compose up -d
- 192.168.83.200:5001