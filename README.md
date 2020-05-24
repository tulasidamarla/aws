# AWS

- AWS is a platform of services. 
  - AWS provides a worldwide infrastructure for computing, networking, and storage capabilities.
  - An IaaS offering that provides virtual machines on-demand: Amazon EC2.
  - Highly distributed storage systems able to scale storage and I/O capacity without limits: Amazon S3
- Common problems such as load balancing, queuing, sending email, and storing files are solved for you by services.
- Picking the right service to build the complex system is key to get benefit out of AWS platform.
- AWS provides api, using which all tasks can be automated, like create networks, start virtual machine clusters, or deploy a relational database.
- Scalability is the key benefit of AWS. AWS can scale from one virtual machine to thousands of virtual machines, storage can grow from gigabytes to petabytes.
- Scalabiltiy is not just about adding, we can remove capacity based on the usage.
- Most of the AWS services are highly available or fault tolerant.
- Services are available on demand, meaning that if we need additonal VM's, AWS provides it in few minutes.
- Global infrastructure
  - AWS offers data centers in North America, South America, Europe, Asia, and Australia.
  - Global infrastructure offers low network latencies between customers and infrastructure, being able to comply with regional data protection requirements, and benefiting from different infrastructure prices in different regions.
- Pricing
  - To calculate monthly the monthly cost of a planned setup, use AWS simple monthly calculator at http://aws.amazon.com/calculator.
  - AWS services are billed in different ways.
    - Based on minutes or hours of usage—A virtual machine is billed per minute. A load balancer is billed per hour.
    - Based on traffic—Traffic is measured in gigabytes or in number of requests, for example.
    - Based on storage usage—Usage can be measured by capacity (for example, 50 GB volume no matter how much you use) or real usage (such as 2.3 GB used).
- Managing servies
  - AWS services can be managed by sending requests to the API manually via web ui called management console, a CLI or programatically via an SDK.
  - VM's can be connected through SSH and can be installed softwares on them.
  - AWS offers lot of services. Some important services are
   EC2—Virtual machines
   ELB—Load balancers
   Lambda—Executing functions
   Elastic Beanstalk—Deploying web applications
   S3—Object store
   EFS—Network filesystem
   Glacier—Archiving data
   RDS—SQL databases
   DynamoDB—NoSQL database
   ElastiCache—In-memory key-value store
   VPC—Private network
   CloudWatch—Monitoring and logging
   CloudFormation—Automating your infrastructure
   OpsWorks—Deploying web applications
   IAM—Restricting access to your cloud resources
   Simple Queue Service—Distributed queues
