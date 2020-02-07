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

**API Reference doc** - https://documenter.getpostman.com/view/2631439/SWE3dfYt

**Sprint task sheet and burndown chart** - https://docs.google.com/spreadsheets/d/1wnaVrKHD61rhG7FJam9kuqD7ZZgLx6cDOj_zbCE6oBY/edit?usp=sharing

![](https://github.com/gopinathsjsu/team-project-cmpe202-team-project/blob/master/burndown_chart.png)
