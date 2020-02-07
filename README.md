Banking System Architecture:<br>

Introduction<br>
Any banking application should be highly secured, scalable, fault tolerant, fast recovery, and consistent databases.<br>
These features could be achieved by designing good architecture. For the purpose of this project, the bank architecture is deployed on AWS cloud.<br>

Resource Components<br>
1) API-Gateway
2) Network Load Balancer
3) Custom Kubernetes Cluster
4) SQS
5) Lambda
5) Cloud Watch
6) MongoDB
<br>

1) API-Gateway is the entry point of the system for reach all services. It is responsible for path routing and securing REST. It Acts as a proxy for the loadbalancer which is in a private subnet and makes it available for public through a VPC link.
2) Network Load Balancer is responsible for acting as proxy and linking up all the microservices which are registered as listeners on different ports.
3) Kubernetes cluster contains 5 pods with 5 different microservices.<br>
   -Transactions service<br>
   -Transfer service<br>
   -Login/Register service<br>
   -Search Transaction service<br>
   -Accounts service<br>
4) Transaction API-Gateway is responsible for securing the transfer and transaction operation.
5) Simple Queue Service is responsible for triggering lambda and maintaing the order of transaction.
6) Lambda Function is responsible for performing transaction related operations on MongoDB. This is the single point for performing any write operation to DB in order to make in consistent.<br>

In order to achieve consistency using NoSQL, the architecture uses combination of SQS and lambda to perform any write operations on MongoDB. MongoDB is 40% less expensive than SQL Database, so it is an attempt to use NoSQL DB to achieve consistentcy. All the write operations are need to be performed only by the lambda. <br>
We are using microservice architecture where each service is deployed on different pods. This makes the system fault tolerant and scalable. Failure of one service will not affect the other services. Moreover, all the services can be written in different APIs.We used Nodejs for Search Transactions, Login, and Accounts API and Go for Transactions, and Transfer API.
To handle large user traffic, we used Network load balancer. API-Gateway is used for securing routes.  

## Architecture Diagram

![Architecture Diagram](https://github.com/kowshhal97/Online-Bank/blob/master/Architecture%20Diagram.jpg)

## Why Kubernetes?

We have 5 microservices to be deployed for our application. During the development phase, the application keeps changing frequently. We initially deployed every service with a combination of Docker host running a container. This docker host was part of an autoscaling group. All the instances were attached to a load balancer.

Issues with this system were

- The heavy overhead for deploying updates.
- Underutilized resources in Docker host.
- Application Rollouts.
- Too many moving parts to manage.
- Time expensive
We later shifted to deploying these services on Kubernetes. It provided us a combination of Docker host, AutoScaling Groups and Loadbalancers. Along with many other features like easy rollouts, resource utilization, ease of use, etc.

Therefore, we just have 1 Kubernetes cluster to manage each microservice.

## Kubernetes Architecture

![Kubernetes Architecture](https://github.com/kowshhal97/Online-Bank/blob/master/KubernetesArc.jpg)


# CAP Theorem-

- C : Consistency

A guarantee that every node in a distributed cluster returns the same, most recent, successful write. Consistency refers to every client having the same view of the data. 

- A : Availibility

Every node that is up returns a response for all read and write requests in a reasonable amount of time. The key word here is every. To be available, every node on (either side of a network partition) must be able to respond in a reasonable amount of time

- P : Partitioning

Our system continues to function and upholds its consistency guarantees in spite of partitions. Network partitions are a fact of life. Distributed systems guaranteeing partition tolerance can gracefully recover from partitions once the partition heals

## CAP theorem in our Architecture-

Our choice of Database was MongoDB as it is a CP database, we needed consistency for our architecture that's the reason we opted for MongoDB, All the writes are strongly consistent, but reads are not always consistent. We wanted to make the system available for reads and consistent for writes, But we can guarentee an eventual consistency for reads.

# AKF scale cube-

**X-axis Scaling:**

 - X-axis scaling or Horizontal duplication refers to running multiple identical copies of the application behind a load  
 balancer. 
 -  We have demonstrated this aspect by running each API on multiple pods scaled by a replica set in a Kubernetes instance. These kubernetes instances have been placed behind an AWS Network Load Balancers .<br/>
     

**Y-axis Scaling:**

 - Y axis scaling refers to functional decomposition of a monolith service , that is breaking one huge service into multiple microservices. <br/>
 - This aspect been implemented by separating all the services to function independently into the following APIs:
  - Login API
  - Transfer API
  - Transaction API
  - Accounts API
  - Search API
<br/>

**Z-axis Scaling:**

 - Z axis scaling refers to splitting similar data among different servers such thaat each server has 1/Nth of the data.<br/>
 - This has been achieved in the case of this application by using MongoDB sharded cluster with 2 config servers, 2 sharded replica sets and 1 mongos query router server. MongoDb has been used to store login, item details, Account details, Transfer details and Searches <br/>

## API Reference doc** - https://documenter.getpostman.com/view/2631439/SWE3dfYt

## Sprint task sheet and burndown chart** - https://docs.google.com/spreadsheets/d/1wnaVrKHD61rhG7FJam9kuqD7ZZgLx6cDOj_zbCE6oBY/edit?usp=sharing

![](https://github.com/gopinathsjsu/team-project-cmpe202-team-project/blob/master/burndown_chart.png)
