# ActiveMQ

- 下载tar包
- 解压到docker文件下
- 进入bin
- activemq可执行命令
- conf配置文件
  - activemq.xml
  - jetty.xml
  - users.properties
  - groups.properties
  - jetty-realm.properties

- 启动

  - bin/activemq start

    ```sh
    INFO: Using default configuration
    (you can configure options in one of these file: /etc/default/activemq /Users/liujiang/.activemqrc)
    
    INFO: Invoke the following command to create a configuration file
    ./activemq setup [ /etc/default/activemq | /Users/liujiang/.activemqrc ]
    
    INFO: Using java '/Library/Java/JavaVirtualMachines/jdk1.8.0_171.jdk/Contents/Home/bin/java'
    INFO: Starting - inspect logfiles specified in logging.properties and log4j.properties to get details
    INFO: pidfile created : '/Users/liujiang/Docker/activemq/apache-activemq-5.9.0/data/activemq-liujiangdeMacBook-Air.local.pid' (pid '79773')
    ```

  - jps查看是否启动

    ```sh
    liujiangdeMacBook-Air:bin liujiang$ jps
    69976 
    79789 Jps
    79773 activemq.jar
    ```

    

  - jetty端口号

    - cat conf/jetty.xml

      ```sh
      <bean id="jettyPort" class="org.apache.activemq.web.WebConsolePort" init-method="start">
                   <!-- the default port number for the web console -->
              <property name="port" value="8161"/>
          </bean>
      ```

      

  - 浏览器访问，ip+端口号

    - <http://localhost:8161/admin>

    - 账户密码，在conf下了jetty-realm.properties

      ```sh
      # username: password [,rolename ...]
      admin: admin, admin
      user: user, user
      ```

      

- 