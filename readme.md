### Set up mongodb cluster

+ Step 1: start the mongo databases

  ```shell
  docker-compose up -d
  ```

  

+ Step 2: exec into one of the mongos:

  ```shell
  docker exec -it localmongo1 /bin/bash
  ```

+ Step 3: access mongo console

  ```
  mongo
  ```

+ Step 4: configure replica set by pasting the following

  ```shell
  rs.initiate({
    _id : 'rs0',
    members: [
      { _id : 0, host : "{YOUR_LOCAL_IP}:27011" },
      { _id : 1, host : "{YOUR_LOCAL_IP}:27012" },
      { _id : 2, host : "{YOUR_LOCAL_IP}:27013" }
    ]
  })
  ```

  

### Config cluster in Springboot application

+ Step1: application.properties

```properties
spring.data.mongodb.uri=mongodb://127.0.0.1:27011,127.0.0.1:27012,127.0.0.1:27013/test?replicaSet=rs0

```

+ Step2:  add `@EnableTransactionManagement` & `MongoTransactionManager`

```java
@EnableTransactionManagement
public class SddmArchiverApplication {

    public static void main(String[] args) {
        SpringApplication.run(SddmArchiverApplication.class, args);
    }

    @Bean
    MongoTransactionManager transactionManager(MongoDbFactory dbFactory){
        return new MongoTransactionManager(dbFactory);
    }
}
```

+ Step3: add `@Transactional` on your functions

```java
    @Transactional(rollbackFor = ArithmeticException.class)
    public Document updateDocument(String id, String collectionName, 					JSONObject content) throws BadRequestException {
    					...
    }
 
```

