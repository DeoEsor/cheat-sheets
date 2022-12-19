# SAI #08: Request-Response Model Deployment - The MLOps Way, Spark - Executor Memory Structure and more...

### Request-Response Model Deployment - The MLOps Way, Model Deployment - Scaling, Spark - Executor Memory Structure, Kafka - Data Replication (Basics).

In this episode we cover:

**MLOps.**

-   Request-Response Model Deployment - The MLOps Way.
    
-   Model Deployment - Scaling.
    

**Data Engineering.**

-   Spark - Executor Memory Structure.
    
-   Kafka - Data Replication (Basics).
    

**No Excuses Data Engineering Project Template.**

-   Making No Excuses Data Engineering Template Bulletproof.

---

### MLOps Fundamentals or What Every Machine Learning Engineer Should Know

---

#### _𝗥𝗲𝗾𝘂𝗲𝘀𝘁-𝗥𝗲𝘀𝗽𝗼𝗻𝘀𝗲 𝗠𝗼𝗱𝗲𝗹 𝗗𝗲𝗽𝗹𝗼𝘆𝗺𝗲𝗻𝘁 - 𝗧𝗵𝗲 𝗠𝗟𝗢𝗽𝘀 𝗪𝗮𝘆._  
 

Last week we looked into a Model Deployment procedure as part of a Streaming Application.  
   
Today we look into deployment of the ML Model as a Request-Response API - The MLOps Way.  
   
𝗟𝗲𝘁’𝘀 𝘇𝗼𝗼𝗺 𝗶𝗻:  
   
𝟭: Version Control: Machine Learning Training Pipeline is defined in code, once merged to the main branch it is built and triggered.  
𝟮: Feature Preprocessing: Features are retrieved from the Feature Store, validated and passed to the next stage. Any feature related metadata that is tightly coupled to the Model being trained is saved to the Experiment Tracking System.  
𝟯: Model is trained and validated on Preprocessed Data, Model related metadata is saved to the Experiment Tracking System.  
𝟰.𝟭: If Model Validation passes all checks - Model Artifact is passed to a Model Registry.  
𝟰.𝟮: Model is packaged into a container ready to be exposed as REST or gRPC API. Model is Served.  
𝟱.𝟭: Experiment Tracking metadata is connected to Model Registry per Model Artifact. Responsible person chooses the best candidate and switches its state to Production.  
𝟱.𝟮: A web-hook is triggered by the action and a new version of containerised API is Deployed. We looked into release strategies in one of the previous posts.  
𝟲: A Request from a Product Application is performed against the API - Features for inference are retrieved from Real Time Feature Serving API and inference results are returned to the Application.  
𝟳: ML APIs are faced with a Load Balancer to enable horizontal scaling.  
𝟴: Multiple ML APIs will be exposed in this way to support different Product Applications. A good example is a Ranking Function.    
𝟵: Feature Store will be mounted on top of a Data Warehouse to retrieve Static Features or  
𝟵.𝟭: Some of the Features will be Dynamic and calculated in Real Time from Real Time Streaming Storage like Kafka.  
𝟭𝟬: An orchestrator schedules Model Retraining.  
𝟭𝟭: ML Models that run in production are monitored. If Model quality degrades - retraining can be automatically triggered.  
   
[𝗜𝗠𝗣𝗢𝗥𝗧𝗔𝗡𝗧]: The Defined Flow assumes that your Pipelines are already Tested and ready to be released to Production. We’ll look into the pre-production flow in future episodes.  
   
𝗣𝗿𝗼𝘀 𝗮𝗻𝗱 𝗖𝗼𝗻𝘀:  
   
✅ Dynamic Features - available.  
✅ Low Latency Inference.  
   
❗️Inference results will be recalculated even if the input or result did not change.  
   
𝗧𝗵𝗶𝘀 𝗶𝘀 𝗧𝗵𝗲 𝗪𝗮𝘆.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Fd22191cc-f2d7-495a-8c16-2ce0dc2c5b60_3662x4665.png)



---

#### _𝗠𝗼𝗱𝗲𝗹 𝗗𝗲𝗽𝗹𝗼𝘆𝗺𝗲𝗻𝘁 - 𝗦𝗰𝗮𝗹𝗶𝗻𝗴._  

 We already know the main types of Machine Learning Model Deployment that you will run into in real life situations: Batch, Embedded into a Stream Application and Request-Response.  
   
Today we look into how we scale these applications given an increase in Data Amount that needs to be processed.  
   
There are two different ways you can scale an Application:  
   
👉 𝗩𝗲𝗿𝘁𝗶𝗰𝗮𝗹 𝗦𝗰𝗮𝗹𝗶𝗻𝗴 - you add resources to the container or server that is running the Application without increasing number of Applications.  
👉 𝗛𝗼𝗿𝗶𝘇𝗼𝗻𝘁𝗮𝗹 𝗦𝗰𝗮𝗹𝗶𝗻𝗴 - you increase the number of Applications that are performing required work.  
  
Let’s  look into what it means for each type of Model Deployment:  
  
𝗕𝗮𝘁𝗰𝗵.  
  
➡️ You Load Data from a Data Warehouse/Lake.  
➡️ Apply Inference.  
➡️ Save results to another Database.  
➡️ Product Applications can use the Inference Results from the Database.  
  
You can scale your Application in two ways.  
  
𝙑𝙚𝙧𝙩𝙞𝙘𝙖𝙡.  
  
👉 You add more resources to the container performing Inference.  
  
✅ You are likely to have your models trained with libraries like scikit-learn, vertical is easy as you will not need to rewrite any code.  
  
𝙃𝙤𝙧𝙞𝙯𝙤𝙣𝙩𝙖𝙡.  
  
👉 You perform Inference by leveraging a Distributed Compute Framework like Spark or Dask.  
  
𝗦𝘁𝗿𝗲𝗮𝗺.  
  
➡️ You Subscribe to a Kafka Topic.  
➡️ Apply Inference in Real Time on new data coming in.  
➡️ Save results to another Kafka Topic.  
➡️ Product Applications subscribe to the Resulting Topic.  
  
Stream Applications will almost always be scaled 𝗛𝗼𝗿𝗶𝘇𝗼𝗻𝘁𝗮𝗹𝗹𝘆.  
  
Scaling is influenced by two factors:  
  
👉 Number of Partitions in the Input Stream - Number of Consumers can’t be more than Number of Partitions. Any excess will Idle.  
👉 Number of Consumers in the Consumer Group Reading from the Input Stream - you increase the number and Consumer Group takes care of rebalancing the Load.  
  
𝗥𝗲𝗾𝘂𝗲𝘀𝘁 - 𝗥𝗲𝘀𝗽𝗼𝗻𝘀𝗲.  
  
➡️ You expose ML Model as a REST or gRPC API.  
➡️ Product Applications are requesting for Inference results directly with the APIs in real time.  
  
A correct way to scale such APIs is 𝗛𝗼𝗿𝗶𝘇𝗼𝗻𝘁𝗮𝗹.  
  
👉 We spin up new ML App containers.  
👉 Wait for containers to become available - the endpoint should start returning Inference results Successfully.  
👉 Register new containers with Load Balancer exposing them to a Product Application.  
   
𝗧𝗵𝗶𝘀 𝗶𝘀 𝗧𝗵𝗲 𝗪𝗮𝘆.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Fcb9b8679-13f3-4981-807e-44c19deb7475_4051x5255.png)

---

### **Data Engineering Fundamentals + or What Every Data Engineer Should Know**

---

#### _𝗦𝗽𝗮𝗿𝗸 - 𝗘𝘅𝗲𝗰𝘂𝘁𝗼𝗿 𝗠𝗲𝗺𝗼𝗿𝘆 𝗦𝘁𝗿𝘂𝗰𝘁𝘂𝗿𝗲._

Changing Spark Executor Memory configuration will most likely be the last step you would be taking to improve your Application Performance.  
  
Nevertheless, it is important to understand if you want to successfully troubleshoot Out Of Memory issues and understand why certain optimizations that you did in your application do not seem to work as expected.  
  
First of all, the entire memory container (JVM Heap Memory) is defined by a well known and widely used property 𝘀𝗽𝗮𝗿𝗸.𝗲𝘅𝗲𝗰𝘂𝘁𝗼𝗿.𝗺𝗲𝗺𝗼𝗿𝘆. It defines memory available for the remaining segments.   
  
There are four major segments that comprise Spark Executor memory, let’s look closer:  
  
𝗥𝗲𝘀𝗲𝗿𝘃𝗲𝗱 𝗠𝗲𝗺𝗼𝗿𝘆:  
  
➡️ This is set to 300MB by default.   
➡️ You can’t change it unless you recompile Spark.  
  
👉 It is used to store Spark internal components  
  
❗️𝘀𝗽𝗮𝗿𝗸.𝗲𝘅𝗲𝗰𝘂𝘁𝗼𝗿.𝗺𝗲𝗺𝗼𝗿𝘆 can’t be less than 1.5 times Reserved Memory.  
  
𝗨𝘀𝗲𝗿 𝗠𝗲𝗺𝗼𝗿𝘆:  
  
➡️ This is set to (JVM Heap Memory - Reserved Memory) * (1 - 𝘀𝗽𝗮𝗿𝗸.𝗺𝗲𝗺𝗼𝗿𝘆.𝗳𝗿𝗮𝗰𝘁𝗶𝗼𝗻).   
➡️ 𝘀𝗽𝗮𝗿𝗸.𝗺𝗲𝗺𝗼𝗿𝘆.𝗳𝗿𝗮𝗰𝘁𝗶𝗼𝗻 defaults to 0.75.  
  
👉 It is used to store user defined structures like UDFs.  
  
𝗦𝗽𝗮𝗿𝗸 (𝗨𝗻𝗶𝗳𝗶𝗲𝗱) 𝗺𝗲𝗺𝗼𝗿𝘆:  
  
➡️ This is set to (JVM Heap Memory - Reserved Memory) * (1 - 𝘀𝗽𝗮𝗿𝗸.𝗺𝗲𝗺𝗼𝗿𝘆.𝗳𝗿𝗮𝗰𝘁𝗶𝗼𝗻).   
  
This segment is further split into two parts.  
  
𝙎𝙩𝙤𝙧𝙖𝙜𝙚 𝙈𝙚𝙢𝙤𝙧𝙮.  
  
➡️ This is set to (Spark (Unified) Memory) * 𝘀𝗽𝗮𝗿𝗸.𝗺𝗲𝗺𝗼𝗿𝘆.𝘀𝘁𝗼𝗿𝗮𝗴𝗲𝗙𝗿𝗮𝗰𝘁𝗶𝗼𝗻  
➡️ 𝘀𝗽𝗮𝗿𝗸.𝗺𝗲𝗺𝗼𝗿𝘆.𝘀𝘁𝗼𝗿𝗮𝗴𝗲𝗙𝗿𝗮𝗰𝘁𝗶𝗼𝗻 defaults to 0.5  
  
👉 It is used to store any Cached or Broadcasted Data if it is configured to be done In Memory.  
  
𝙀𝙭𝙚𝙘𝙪𝙩𝙞𝙤𝙣 𝙢𝙚𝙢𝙤𝙧𝙮.  
  
➡️ This is set to (Spark (Unified) Memory) * (1 - 𝘀𝗽𝗮𝗿𝗸.𝗺𝗲𝗺𝗼𝗿𝘆.𝘀𝘁𝗼𝗿𝗮𝗴𝗲𝗙𝗿𝗮𝗰𝘁𝗶𝗼𝗻)  
  
👉 It is used to store any intermediate Data created by execution of Spark Jobs.  
  
❗️Boundary between Storage and Execution memory is flexible.   
❗️Execution Memory can always borrow memory from storage fraction.   
❗️Storage Memory can only borrow from Execution if it is not occupied.  
❗️If Execution Memory has borrowed from storage - Storage Memory can only reclaim it after it was released by Execution.  
❗️Execution Memory can forcefully evict data from Storage Memory and claim it for itself.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2F21e78360-b8f1-4ccd-9319-400c3fa05ec3_4180x3993.png)

---

#### _𝗞𝗮𝗳𝗸𝗮 - 𝗗𝗮𝘁𝗮 𝗥𝗲𝗽𝗹𝗶𝗰𝗮𝘁𝗶𝗼𝗻 (𝗕𝗮𝘀𝗶𝗰𝘀)._  

Data Replication in Kafka is a mechanism that sits at the core of its reliability and durability guarantees - I am going to dedicate more than one post to explain the concepts behind it. As a Data Engineer you 𝗠𝗨𝗦𝗧 understand this deeply as it will impact the behavior of your Applications.  
  
Let's look into how replication works in Kafka.  
  
𝗦𝗼𝗺𝗲 𝗿𝗲𝗳𝗿𝗲𝘀𝗵𝗲𝗿𝘀:  
  
➡️ Kafka Cluster is composed of Brokers.  
➡️ Data is stored in Topics that can be compared to tables in Databases.  
➡️ Topics are composed of Partitions.  
➡️ Clients writing Data to Kafka are called Producers.  
➡️ Clients reading Data from Kafka are called Consumers.  
➡️ Each Partition is and behaves as a Write Ahead Log - Producers always write to the end of a Partition.  
  
➡️ Kafka Cluster uses Zookeeper as an external central metadata store (being actively deprecated in favor of KRaft protocol for managing metadata internally).  
  
𝗗𝗮𝘁𝗮 𝗥𝗲𝗽𝗹𝗶𝗰𝗮𝘁𝗶𝗼𝗻:  
  
➡️ Kafka is a Distributed System which means that Partitions of a single Topic will most likely and should be spread across different Brokers.  
➡️ Replication is a procedure when Partitions of Topics will be replicated a number of times.  
  
👉 Replication factor is configured using 𝗿𝗲𝗽𝗹𝗶𝗰𝗮𝘁𝗶𝗼𝗻.𝗳𝗮𝗰𝘁𝗼𝗿 or 𝗱𝗲𝗳𝗮𝘂𝗹𝘁.𝗿𝗲𝗽𝗹𝗶𝗰𝗮𝘁𝗶𝗼𝗻.𝗳𝗮𝗰𝘁𝗼𝗿 configuration - per topic and default for automatically created topics respectively.  
  
➡️ One of the partition replicas is defined as a Leader Partition.  
➡️ Data is always written to the Leader Partition and then is replicated to the followers.  
➡️ In most cases Data is also Read from Leader Partitions only. This means that replicas are only used for reliability reasons.  
➡️ Partition replicas can be in-sync or out of sync with the Leader Partition. In-sync means that Replica:  
  
👉 Has an active session with Zookeeper.  
👉 Fetched messages from the Leader Partition in the last n seconds (n is configurable).  
👉 Had no lag behind the Leader Partition at least once in the last n seconds (n is configurable).  
  
➡️ Partition replicas are used in case of an emergency shutdown of a broker or a planned upgrade when a downtime is needed.  
➡️ If a Broker containing Leader Replicas goes offline - a new in-sync Replica will be elected as a new Leader retaining the normal operation of the Topic.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Ffc780fc7-cda1-4e99-9a89-7d01cfa1a577_3978x4989.png)

---

### Making No Excuses Data Engineering Project Template Bulletproof

---

Today we look into how we could make the entire 𝗦𝘆𝘀𝘁𝗲𝗺 of 𝗧𝗵𝗲 𝗧𝗲𝗺𝗽𝗹𝗮𝘁𝗲 more robust.  
   
Here are some points 𝗬𝗼𝘂 should take into consideration and 𝗪𝗲 will build into the implementation of 𝗧𝗵𝗲 𝗧𝗲𝗺𝗽𝗹𝗮𝘁𝗲 when bringing it to life:  
   
➡️  Make 𝗖𝗼𝗹𝗹𝗲𝗰𝘁𝗼𝗿 (2.) and 𝗘𝗻𝗿𝗶𝗰𝗵𝗺𝗲𝗻𝘁 𝗔𝗣𝗜 (4.) 𝗛𝗶𝗴𝗵𝗹𝘆 𝗔𝘃𝗮𝗶𝗹𝗮𝗯𝗹𝗲 - front the application with a 𝗟𝗼𝗮𝗱 𝗕𝗮𝗹𝗮𝗻𝗰𝗲𝗿 and deploy multiple replicas of The Collector.  
   
👉 Bonus points for introducing autoscaling.  
   
➡️  Ensure that each Application that reads from Kafka (3., 5., 6.) can do so using multiple instances of application deployed simultaneously.  
   
👉 We already covered 𝗖𝗼𝗻𝘀𝘂𝗺𝗲𝗿 𝗚𝗿𝗼𝘂𝗽𝘀 in one of the episodes. We will use them.  
   
➡️  Implement centralized logging for your Real Time Applications (2.- 6.).   
   
👉 Use FluentD sidecar containers that would pass application logs to a separate index in ElasticSearch (T5.).  
👉 Mount  Kibana on top of Elasticsearch for easy Log Access.  
   
➡️  Implement centralized Application Metric collection for any Python application (2.- 6.).  
   
👉 Use Prometheus Server (T7.) to collect metrics from the applications.  
👉 Mount Grafana on top of Prometheus for convenient Metrics exploration.  
👉 We will also use these metrics to implement autoscaling.  
   
➡️  Implement 𝗔𝗹𝗲𝗿𝘁𝗶𝗻𝗴 on data in Dead Letter Topics.  
   
👉 Use either a separate Stream Processing Application or pipe data to Elasticsearch for Real Time Access and calculate alerting metrics on an interval.  
    
All of the above are a minimum must haves for a robust and stable system. In some of the next episodes we will be doing deeper dives into separate components of The Template top-down so 𝗞𝗲𝗲𝗽 𝘁𝘂𝗻𝗲𝗱 𝗶𝗻!


![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Feeb98463-b459-4246-a783-f68e259bc726_3985x5777.png)