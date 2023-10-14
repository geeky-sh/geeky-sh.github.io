---
layout: post
title: Dissecting AWS Copilot
description: Understanding what AWS Copilot works under the hood
---

[AWS copilot](https://aws.github.io/copilot-cli/) is a open-source tool built by the AWS team to containerized services on AWS.

For someone who gets overwhelmed on learning Cloud Infrastructure using Terraform, Cloudformation templates & using multiple services for load balancing, logging, secrets management, autoscaling, monitoring, CD pipelines etc, copilot can be a single go-to tool to manage all this in a simpler manner and provides a quicker way to achieve all this as well. However the major downside is that it is built only for managing containerized applications on AWS. It is a trade-off and you need to decide which solution suits you better at a particular moment.

In this post, we will try to understand what copilot builds and how it works under the hood. We will try to get a high level understanding of the purpose & working of each of the services.

## Assumptions
- You are using third-party DNS provider like Godaddy, namecheap, etc. i.e. you are not using AWS Route53
- You are using `Load Balanced Service` type while creating Copilot service.
- You are using Fargate to create your tasks in ECS
- You have a basic understanding of what is ECS, how it works, etc
- You know what an application server is.


## Architecture
![Architecture Diagram](/images/copilot-arch.png)

## Terms
- **LB** - Load Balancer

## Request Flow
As soon as client sends a request to the server using the API, we will now go through what happens under the hood considering that your service is deployed via Copilot. Copilot allows the abilty to manage all the below resources using its cli-tool.

When a client sends a request using any device (browser, cli-tools, mobile, etc), the following happens:
- In order to get the IP address, client will ask DNS
- DNS will respond with set the set of IP addresses which can be used to send the request. These IP addresses belong to the LB
- The request goes to LB, which determines the underlying instance to send the request to. This instance is runnning the actual application server.
- This server takes in the request, uses DB and/or cache whatever is relevant to compute the response, logs the relevant messages required, formulates the response and returns it.

## AWS Services & Their Usage
### Target Group
- A logical grouping of tasks / instances which has the same application code running.
- It also has common set of properties like routing algorithm, server port, etc.
- It is primarily used for autoscaling. Whenever a service scales up, more tasks are added to the target group and vice-versa.

### Load Balancer
- It is the public facing endpoint where all the client requests arrive.
- It has the ability to define rules for routing the request. This is generally based on port, path & hostname.
- On the first level, target group is selected based on the listener rules defined in LB
- In the next level, the actual target is selected based on the routing algorithm defined in the Target Group.
- You can use single Load Balancer to serve multiple requests with different hosts / subdomains. The routing logic to lead to the right service is defined in the first level (above)

### Certificate Manager (ACM)
- This utility is used to manage all the SSL certificates corresponding to the different public service endpoints we use.
- Load Balancer also contains the map of all these certificates it needs to use while validating and responding to the request.
- Adding or Modifying certificates is not provided via Copilot. It needs to be done manually. It only provides the ability to refer to a certificate while configurating a service.

### Service Discovery
- This Tool provides human readable strings to identify each service.
- It used AWS Cloudmap under the hood to determine the right instance / task to direct the request to.

### Parameter Store (Systems Manager)
- It is used to store secrets that are used in the application like `DB_PASSWORDS`, `GOOGLE_CLIENT_SECRET`, etc
- We can define in copilot `manifest.yml` which `ENV` variable should be stored as a secret.

### Cloudwatch
- It stores the logs correponding to each of the services & provides the ability to search, filter & gather insights through them.
- It is used to define alerts which are typically used as autoscaling triggers.
- Copilot manages all of this internally via the configurations provided in `manifest.yml` file.

## References
- [Service Discovery](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-discovery.html)
- [AWS Cloud Map](https://docs.aws.amazon.com/cloud-map/latest/dg/what-is-cloud-map.html)
- [Load Balancers & Target Group](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
- [AWS Copilot Documentation](https://aws.github.io/copilot-cli/docs/overview/)
