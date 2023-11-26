# Infrastructure

## AWS Zones
Identify your zones here

## Servers and Clusters

### Table 1.1 Summary
|------------------|--------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| VPC	           | Local network in AWS     | N/A 																   | 2                                                        		 | We have 2 VPC's, one in us-east-1 and the other one in us-west-1 for redundancy puposes in case one of them will fail     |
| EC2 VM           | Compute resources        | t3.micro															   | 6 																 | This asset will be created in 2 regions, 3 VM's in one VPC, 3 VM's in the second VPC, each VM in a separate az (for region redundancy)
| EKS Cluster      | Containerised environment| N/A - contains EC2 as compute power (t3.medium)                        | 2                                                               | THE EKS cluster will contain 2 nodes for internal resiliency and will have 1 EKS cluster in each region
| ALB              | Entrypoint in the AWS    | N/A                                                                    | 2                                                               | We will have one ALB in each region which will be redundant in case one region will fail
| SQL cluster (RDS)| Database                 | N/A                                                                    | 4                                                               | We will have 2 clusters, each cluster with 2 instances

### Descriptions
VPC - Is the local LAN in AWS where can create the needed resources
EC2 VM - Is is the AWS Virtual Machine which will act as a compute power for the applications that we want to deploy
EKS Cluster - Is it the AWS Container Orchestrator and serve as compute platform for the containerised applications
ALB - AWS application load balancer which server as a entrypoint against our application deployed in EKS/EC2
SQL Cluster - database for storing different data of the deployed applications

## DR Plan
### Pre-Steps:
1. Create the VPC on the second region
2. Create the EKS Cluster and add nodegroups containing EC2 instances and also create EC2 instances if required
3. Create the RDS cluster
4. Be sure that the above infra is working as expected

## Steps:
Create a cloud load balancer and point DNS to the load balancer. This way you can have multiple instances behind 1 IP in a region. During a failover scenario, you would fail over the single DNS entry at your DNS provider to point to the DR site. This is much more intelligent than pointing to a single instance of a web server.
Have a replicated database and perform a failover on the database. While a backup is good and necessary, it is time-consuming to restore from backup. In this DR step, you would have already configured replication and would perform the database failover. Ideally, your application would be using a generic CNAME DNS record and would just connect to the DR instance of the database.