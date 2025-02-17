1. **Write a Terraform code to create multiple S3 buckets**
```
provider "aws" {
  region = "us-east-1"
}

variable "s3_buckets" {
  type    = list(string)
  default = ["bucket-1", "bucket-2", "bucket-3"]
}

resource "aws_s3_bucket" "buckets" {
  count  = length(var.s3_buckets)
  bucket = var.s3_buckets[count.index]

  tags = {
    Name        = var.s3_buckets[count.index]
    Environment = "Development"
  }
}
```
2. **How you managed statefile**
In Terraform, the state file (terraform.tfstate) is managed in two ways:
**Local State (Default)** Stored in the local directory where Terraform is run.
**Remote State**  Stored in a remote backend like AWS S3 with DynamoDB for state locking.
**example**
```
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
    encrypt        = true
  }
}
```


3. **So how are you managing the conflict? State file conflicts**

Terraform manages state file conflicts using state locking, which prevents multiple users from making concurrent changes.

**How Conflict is Managed?**

**DynamoDB for State Locking (AWS S3 Backend)**
When Terraform runs, it acquires a lock in DynamoDB to prevent concurrent modifications.
If another user tries to modify the state simultaneously, they must wait for the lock to be released.

5. what you did with Jenkins?
6. "**How are you integrating the SonarQube with the Jenkins server?**"
   - Install SonarQube Plugin in Jenkins
   - Configure SonarQube in Jenkins
   - Configure Sonar Scanner in Jenkins
   - Add SonarQube to a Jenkins Pipeline
   - Verify SonarQube Analysis
   **Best Practices**
     - Ensure SonarQube Server and Jenkins can communicate.
     - Use webhooks in SonarQube for quality gate checks.
     - Store credentials securely using Jenkins credentials manager.

7. **How were you authenticating Jenkins to push docker image to registery?**
   1. Store Docker Registry Credentials in Jenkins
   2. Use Credentials in a Jenkins Pipeline
***For AWS ECR Authentication***
   - Store AWS credentials in Jenkins (aws-ecr-credentials).
   - Modify pipeline:
*Best Practices*
   - Use Jenkins credentials store instead of hardcoding secrets.
   - Use Docker access tokens instead of passwords.   
8. Have you worked on the Kubernetes?So what deployment strategy are you following?
9. ***So how are you implementing the blue green deployment?***
Blue-Green Deployment ensures zero downtime by running two versions (blue = current, green = new) and switching traffic when the new version is verified.
* Deploy Two Versions (Blue & Green)
   We deploy both versions, keeping blue active while preparing green.
* Use a Service to Switch Traffic
  The Service is initially pointing to the blue version.
* Deploy the Green Version & Test
10. ***Do you know what is HPA?***
Yes! HPA (Horizontal Pod Autoscaler) is a Kubernetes feature that automatically scales the number of pods in a Deployment, StatefulSet, or ReplicaSet based on CPU, memory, or custom metrics.

11. ***suppose you deploy one application okay and you found some issue, you wanted to roll back using the kubernetes how you roll back to the particular version, what is the command?***
In Kubernetes, you can roll back a Deployment to a previous version using the kubectl rollout undo command.
``` kubectl rollout undo deployment <deployment-name> ```
12. ***What is the stateful set in the Kubernetes?***
A StatefulSet in Kubernetes is a workload API object designed to manage stateful applications. It ensures that each pod has a stable identity and persistent storage, which is useful for applications that require consistent network identities, such as databases.
  - Stable Network Identity
  - Persistent Storage
  - Ordered Deployment and Scaling
13. Have you worked on the AWS, right?
14. ***So how many types of policy, IAM policy are there? IAM policies?***
 - ***Identity-based policies:*** These are attached to IAM identities (users, groups, or roles).
 - ***Resource-based policies:*** These are attached directly to AWS resources (such as S3 buckets, Lambda functions, or SQS queues). They define who can access the resource and what actions they can perform.
 - ***Permissions boundaries:*** These are advanced policies used to set the maximum permissions a role or user can have.
 - ***Organizations SCP (Service Control Policies):*** These are used in AWS Organizations to set permissions across accounts in the organization. SCPs define what actions are allowed or denied for accounts, organizational units, or the entire organization.

15. ***So what is the difference between the S3 bucket policies and acls?***
**S3 Bucket Policies:**
Scope: Apply at the bucket level (or optionally to objects within the bucket).
Granularity: Provide more fine-grained control over permissions. You can specify actions (like s3:GetObject, s3:PutObject), resources (e.g., specific objects or buckets), and conditions (like IP address, user agent, or time).
Usage: Typically used for more complex permission scenarios, such as granting cross-account access or enforcing restrictions on actions (e.g., allowing access only from certain IP addresses).

***ACLs (Access Control Lists):***
Scope: Apply at both the bucket and object level.
Granularity: Provide more limited control compared to bucket policies. ACLs grant predefined permissions, such as READ, WRITE, FULL_CONTROL on buckets or objects. You cannot specify actions or conditions like in bucket policies.
Usage: Often used for simpler access control, like enabling public access to objects or providing access to a specific user or group.


16. ***what is the dynamic auto scaling?***
Dynamic Auto Scaling in AWS (or any cloud platform) refers to the ability to automatically adjust the number of compute resources (like EC2 instances) in response to changing demand. This helps ensure that your application always has the right amount of resources to handle the load, without over-provisioning or under-provisioning.
***Key Features:***
- Automatic Adjustment
- Scaling In/Out
- Policies & Metrics
- Elastic Load Balancer (ELB) Integration

17. ***What is the difference between Security groups and NACL?***
both Security Groups (SGs) and Network Access Control Lists (NACLs) are used to control traffic to and from resources within a Virtual Private Cloud (VPC).
- ***Security Groups (Stateful)***: Stateful: Security groups are stateful, meaning they automatically allow response traffic for any request that was allowed inbound. If you allow an inbound request (e.g., from a specific IP), the return traffic is automatically allowed, even if you don't explicitly permit outbound traffic.
        ***Scope:*** Security groups are applied to individual instances (e.g., EC2 instances) or resources like Load Balancers, RDS instances, etc.
- ***Network Access Control Lists (NACLs) (Stateless)***: NACLs are stateless, meaning each request is evaluated based on the rules defined in the NACL. If you allow inbound traffic, you also need to explicitly allow outbound traffic for the response. There's no automatic return traffic allowance like with security groups.
    ***Scope:*** NACLs are applied at the subnet level, meaning they control traffic going in and out of the subnet where resources reside (rather than at the instance level).

18. ***how will you manage this and see your information like credentials and API keys in Docker containers?***
To securely manage credentials and API keys in Docker containers, follow these best practices:  

**Use Environment Variables (Not Hardcoded)**
   - Pass secrets via `-e` flag or `docker-compose.yml` instead of hardcoding them.  
   - Example:  
     ```bash
     docker run -e API_KEY=your_secret_key myapp
     ```

**Use Docker Secrets (For Swarm & Kubernetes)**
   - Store sensitive data in Docker secrets instead of environment variables.  
   - Example:  
     ```bash
     echo "your_secret_key" | docker secret create my_secret_key -
     ```

**Use `.env` Files (But Exclude from Version Control)**
   - Store credentials in an `.env` file and use `docker-compose`.  
   - Example `.env` file:  
     ```env
     API_KEY=your_secret_key
     ```
   - **Ensure `.env` is added to `.gitignore`**.

**Use AWS Secrets Manager or Vault**
   - Fetch credentials dynamically using cloud services like AWS Secrets Manager, HashiCorp Vault, or Parameter Store.  
   - Example: Use AWS SDK inside your app to retrieve secrets dynamically.

**Bind Mount Secrets at Runtime**
   - Mount a file containing secrets instead of passing them as environment variables.  
   - Example:  
     ```bash
     docker run -v /path/to/secrets:/secrets myapp
     ```

**Scanning for Secrets**
   - Use tools like `truffleHog`, `git-secrets`, or `Docker Scout` to scan for exposed secrets.  
   - Example:  
     ```bash
     trufflehog --regex --entropy=True .
     ```

**Rotate and Expire Credentials Regularly**
   - Regularly rotate API keys and credentials.  
   - Use short-lived tokens where possible (e.g., AWS STS, OAuth tokens).

19. **how do you ensure high availability and scalability for applications deployed in Kubernetes?**

**High Availability (HA):**  
   - **Multi-node Cluster**: Deploy across multiple nodes to prevent single points of failure.  
   - **ReplicaSets**: Ensure multiple pod replicas are running to handle failures.  
   - **Pod Anti-Affinity**: Spread pods across different nodes for resilience.  
   - **Load Balancing**: Use **Service (ClusterIP, NodePort, LoadBalancer, or Ingress)** to distribute traffic.  
   - **Persistent Storage**: Use **StatefulSets** with Persistent Volumes (PVs) for stateful workloads.  
   - **Control Plane HA**: Run multiple control plane nodes to avoid API server downtime.  

**Scalability:**  
   - **Horizontal Pod Autoscaler (HPA)**: Scales pods based on CPU, memory, or custom metrics.  
   - **Cluster Autoscaler**: Adjusts the number of worker nodes dynamically.  
   - **Ingress with Load Balancer**: Manages incoming traffic efficiently.  
   - **Efficient Resource Requests/Limits**: Optimize CPU and memory to prevent over/under-utilization.  
   - **CDNs & Caching**: Use **Redis, CloudFront, or Nginx** for performance enhancement.  

20. ***How do you configure artifactory for both Maven and Docker repositories?***

 **Set Up Maven Repository**  
1. **Login to Artifactory UI** → Go to **Admin** → **Repositories** → **Local**.  
2. Click **New Local Repository** → Select **Maven**.  
3. Configure:  
   - **Repository Key**: `maven-local`  
   - **Package Type**: `Maven`  
   - Enable **Snapshot** and **Release** if needed.  
4. Click **Save & Finish**.  

**Maven Settings.xml Configuration:**  
```xml
<repository>
    <id>artifactory-maven</id>
    <url>http://<ARTIFACTORY_URL>/artifactory/maven-local</url>
</repository>
```
Use `mvn deploy` to push artifacts.

---

**Set Up Docker Repository**  
1. **Enable Docker Support**: In **Admin** → **Repositories** → **Local** → Create **Docker Repository**.  
2. Configure:  
   - **Repository Key**: `docker-local`  
   - **Package Type**: `Docker`  
   - Enable **V2 registry**  
3. Click **Save & Finish**.  

**Docker Daemon Configuration:**  
1. Login to Artifactory:
   ```sh
   docker login <ARTIFACTORY_URL>
   ```
2. Tag & Push Image:
   ```sh
   docker tag my-image <ARTIFACTORY_URL>/docker-local/my-image:latest
   docker push <ARTIFACTORY_URL>/docker-local/my-image:latest
   ```
21. How do you handle challenges in meeting deadlines and managing dependencies across various teams?
22. ***How do you manage branching and merging strategies?***
**Branching Strategies**
   - **Main Branches:**
     - `main` (or `master`): Stable production-ready code.
     - `develop`: Integration branch where features are merged before release.
   - **Supporting Branches:**
     - `feature/*`: Used for developing new features.
     - `bugfix/*`: Used to fix bugs before merging into `develop`.
     - `release/*`: Prepares a new release, allowing final testing.
     - `hotfix/*`: Urgent fixes applied directly to `main` and back-merged.

**Merging Strategies**
   - **Fast-Forward Merge**: Used when there are no diverging changes.
     ```bash
     git merge --ff
     ```
   - **Squash Merge**: Combines multiple commits into a single commit.
     ```bash
     git merge --squash
     ```
   - **Rebase**: Moves feature branch commits on top of the latest `main`.
     ```bash
     git rebase main
     ```
   - **Merge Commit**: Creates a new commit to merge branches.
     ```bash
     git merge --no-ff
     ```

23. ***how do you secure sensitive data credentialing data repositories? In the GitHub repositories?***

**Never Commit Credentials Directly**
- Avoid storing API keys, passwords, or secrets in the repository.
- Use `.gitignore` to exclude sensitive files (e.g., `.env` files).

**Use GitHub Secrets**
- Store sensitive data in **GitHub Actions Secrets** for workflows.
- Use environment variables to access secrets securely.

**Use Environment Variables**
- Load credentials from environment variables instead of hardcoding them.

**Implement Git Hooks for Pre-Commit Checks**
- Use tools like `pre-commit`, `git-secrets`, or `truffleHog` to prevent accidental commits of sensitive data.

**Encrypt Sensitive Data**
- Store credentials in encrypted vaults like **AWS Secrets Manager, HashiCorp Vault, or SOPS**.
- Decrypt them only at runtime.

**Enable Repository Security Features**
- Enable **GitHub Advanced Security** for secret scanning.
- Use **branch protection rules** and require reviews for sensitive changes.

**Rotate Credentials Regularly**
- Periodically update and rotate API keys, passwords, and access tokens.

**Monitor & Audit**
- Use **GitHub Secret Scanning** to detect exposed secrets.
- Set up logging and alerts for unauthorized access attempts.

**Use IAM & Least Privilege Access**
- Implement **least privilege** access for repository permissions.
- Use **role-based access control (RBAC)** to restrict access.

24. What is your experience with the GitHub Actions?
25. How do you integrate, code quality checks?
26. What kind of custom metrics did you monitor using CloudWatch, and how did you help you proactively identify issues?
27. Have you used AWS event bridge with lambda and what was it for?
28. What are some best practices you follow to ensure that your DevOps pipelines are scalable, maintainable, and efficient?
29. what is, one of the biggest challenges you faced in automating CICD and infrastructure provisioning, and how did you overcome it?
30. Explain the difference between the EC 2 instances instance types.
31. How do you troubleshoot the failing pod in Kubernetes?
32. How do you document your infrastructure and automation scripts? 
33. How do you use, Ansible Vault to connect secrets?
34. how do you store your secrets in AWS secret manager?
35. how do you stay updated with the new dev tools and technologies, and how do you decide which ones to integrate into your workflows?
36. Can you explain a situation where you had to lead a project or an initiative within a DevOps team?
37. what is the difference between a deployment daemonset and statefulset in Kubernetes?
38. Can you share your screen and write the manifest file Deployment?
39. how do you configure a Jenkins pipeline for a containerized application?
40. how do you manage a state in Terraform?
41. What are the Terraform models, sir, and why are they used?
42. What files you mention in the modules?
43. What is,an Ansible playbook and how it is structured?
44. How do you set up alerting in a Prometheus?
45. Can you explain how Grafana integrates with Prometheus, for visualization?
46. Do you have any experience in bash script?
47. What what about Python?
48. Jenkins pipeline is stuck on a particular stage. How do you debug it?
49. Your terraform apply command is failing. Okay. What are the steps to prepare for troubleshooting?
50. An EC2 instance is unable to connect to an RDS database. What could be the issue?
51. How you will update the version in RDS? In terraform.
52. how you handle the situation where the deployment fails in production?
53. What about the Bluegreen deployment?Have you ever come up with it? how we switch the traffic?
54. Can you explain the role of Docker in your CICD pipelines? Like, how do you ensure the security of containers in your workflow?
55. Can you provide an example where you identified and fixed the security vulnerability using these tools within the pipeline?
56. Can you expand your experience with the AWS services.
57. What do you know about API Gateway?
58. What all AWS services you have worked on.
58) Write a Terraform code to provision EC2 and how will you refer to the EC2 instance id created, write an Ansible playbook for installing python on the EC2 server that is created.
59) Tell us about Disaster recovery strategies.
60) What all Kubernetes services you have used 
61) What all monitoring tools have you used.
62) Write a bash script to read the last 10 lines of a file and how to get lines having word "out of memory''.
63) How service mesh is used.
64) How have you optimized codes.
65) What security tools are you using.
66) If you have exhausted all ip addresses of your vpc,what will you do
# Service mesh

67) Main components of servicemesh.
68) What problem does service mesh solve in microservice architecture.
69) What is the role of sidecars in servicemesh.
70) How does servicemesh improve communication between microservices.
71) How is servicemesh helping in deployments.
72) How does servicemesh handles multi cloud practitioner multi cluster environments.
73) What is a process of configuring servicemesh in Kubernetes env.
74) How does servicemesh differs from traditional load balancing and traditional networking.
75) What do you understand by servicemesh.
