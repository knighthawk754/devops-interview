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

```
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-terraform-s3-bucket-12345"  # Replace with a unique name
  acl    = "private"

  tags = {
    Name        = "MyTerraformS3Bucket"
    Environment = "Dev"
  }
}

# Enable Versioning
resource "aws_s3_bucket_versioning" "versioning" {
  bucket = aws_s3_bucket.my_bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}

# Lifecycle Rule - Deletes objects after 30 days
resource "aws_s3_bucket_lifecycle_configuration" "lifecycle" {
  bucket = aws_s3_bucket.my_bucket.id

  rule {
    id     = "delete-old-objects"
    status = "Enabled"

    expiration {
      days = 30
    }
  }
}

# Enable Server-Side Encryption
resource "aws_s3_bucket_server_side_encryption_configuration" "encryption" {
  bucket = aws_s3_bucket.my_bucket.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

output "s3_bucket_name" {
  value = aws_s3_bucket.my_bucket.id
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
6. "**How are you integrating the SonarQube with the Jenkins server?"
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

| Feature | Bucket Policies | Access Control Lists (ACLs) |
|---|---|---|
| **Scope** | Apply to the entire bucket and all objects within it (unless overridden by object ACLs) | Apply to individual buckets or objects |
| **Policy Language** | Uses JSON-based policy language, offering fine-grained control | Uses a predefined set of grants (read, write, full control) |
| **Granularity** | Highly granular, allowing control based on user, IP address, time, and other conditions | Less granular, limited to predefined permissions |
| **Complexity** | More complex to write and manage, but offers greater flexibility | Simpler to use for basic access control |
| **Management** | Managed at the bucket level | Managed at the bucket or object level |
| **Capabilities** | Supports complex conditions, including IAM roles, user groups, and IP address ranges | Primarily for simple grant/deny access |
| **Permissions Inheritance** | Policies apply to all objects unless overridden by object ACLs | Object ACLs override bucket ACLs and policies |
| **Use Cases** | Complex access control scenarios, cross-account access, restricting access based on conditions | Quick and simple access grants, legacy systems |
| **Default Setting** | By default, buckets and objects are private. Bucket policies must be explicitly attached. | By default, buckets and objects are private. ACLs must be explicitly granted. |
| **Control over Principal** | Defines what principals(users, services) can do. | Defines who(principals) can do what. |
| **Recommended Usage** | AWS recommends using bucket policies for most access control scenarios. | AWS recommends using bucket policies and IAM roles instead of ACLs where possible. ACLs are considered a legacy access control mechanism. |



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
     ```
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
27. **Have you used AWS event bridge with lambda and what was it for?**
    Kubernetes (K3s) CI/CD pipeline, we used EventBridge to trigger a Lambda function whenever an ArgoCD deployment failed. The Lambda function analyzed logs, notified the 
    DevOps team via SNS/Slack, and attempted an automatic rollback.
    Another use case was security monitoring, where EventBridge captured IAM unauthorized access events from CloudTrail and invoked a Lambda function to block suspicious 
    IPs using AWS WAF.
29. What are some best practices you follow to ensure that your DevOps pipelines are scalable, maintainable, and efficient?
### 1. **Modular & Reusable Pipelines**
   - Use **templated workflows** (e.g., GitHub Actions, GitLab CI templates) for consistency.
   - Parameterize pipelines for flexibility across environments.

### 2. **Infrastructure as Code (IaC)**
   - Use **Terraform/CloudFormation** for infrastructure provisioning.
   - Maintain **version-controlled IaC** to ensure reproducibility.

### 3. **Parallel & Asynchronous Execution**
   - Optimize pipeline execution with **parallel jobs**.
   - Use **event-driven triggers** (AWS EventBridge, SQS) to reduce idle wait times.

### 4. **Security & Compliance**
   - Implement **SAST, DAST, and dependency scanning** in CI/CD.
   - Enforce **least privilege IAM roles** for pipeline services.

### 5. **Observability & Logging**
   - Use **CloudWatch, ELK, Prometheus** for real-time monitoring.
   - Implement structured logging for easier debugging.

### 6. **Automated Testing & Quality Gates**
   - Integrate **unit, integration, and security tests**.
   - Enforce **SonarQube-based quality gates** before deployment.

### 7. **Scalability & Self-Healing**
   - Use **autoscaling runners (K8s, EC2 Auto Scaling)**.
   - Implement **circuit breakers & rollback mechanisms** for failed deployments.

### 8. **Efficient Artifact & Dependency Management**
   - Use **caching mechanisms (Docker layer caching, Nexus, Artifactory)**.
   - Store reusable artifacts for faster builds.
31. what is, one of the biggest challenges you faced in automating CICD and infrastructure provisioning, and how did you overcome it?
32. Explain the difference between the EC2 instances instance types.
### 1. **General Purpose (Balanced Performance)**
   - **T-Series (T3, T4g)** – Burstable CPU, cost-efficient for small workloads.  
   - **M-Series (M6g, M7i)** – Balanced compute, memory, and network; ideal for most applications.  

### 2. **Compute Optimized (High CPU Performance)**
   - **C-Series (C6g, C7i)** – Optimized for compute-heavy tasks like high-performance computing (HPC) and batch processing.  

### 3. **Memory Optimized (High RAM)**
   - **R-Series (R6g, R7i)** – Ideal for databases and in-memory caching.  
   - **X-Series (X2idn, X2gd)** – High RAM for large-scale in-memory workloads.  

### 4. **Storage Optimized (High IOPS)**
   - **I-Series (I4i)** – Fast NVMe SSDs for low-latency databases.  
   - **D-Series (D3, D3en)** – Large HDD storage for big data analytics.  

### 5. **Accelerated Computing (GPU & FPGA)**
   - **G-Series (G5, G6g)** – GPU instances for AI/ML and video rendering.  
   - **P-Series (P4, P5)** – High-performance GPUs for deep learning.  
   - **F-Series (F1)** – FPGA-based for custom hardware acceleration.  

### **Choosing the Right Instance**
- **Web Apps & Small Workloads** → **T3, M6g**  
- **High CPU Processing** → **C7i**  
- **Large Databases** → **R6g, X2idn**  
- **Big Data & Analytics** → **D3, I4i**  
- **AI/ML & GPU Workloads** → **G5, P5**  
33. How do you troubleshoot the failing pod in Kubernetes?
### 1. **Check Pod Status**
   ```
   kubectl get pods -n <namespace>
   ```
   - Look for **CrashLoopBackOff, ImagePullBackOff, or Pending** statuses.  

### 2. **Describe the Pod for More Details**
   ```
   kubectl describe pod <pod-name> -n <namespace>
   ```
   - Check for **events, resource limits, liveness probe failures, or scheduling issues**.  

### 3. **Check Logs for Errors**
   ```
   kubectl logs <pod-name> -n <namespace> --previous
   ```
   - Helps identify **application crashes or misconfigurations**.  

### 4. **Check for Resource Issues**
   ```
   kubectl top pod <pod-name> -n <namespace>
   ```
   - Verify if **CPU/memory limits are exceeded**.  

### 5. **Check Node & Events**
   ```
   kubectl get nodes
   kubectl get events -n <namespace>
   ```
   - Identify **node failures, taints, or network issues**.  

### 6. **Exec into the Pod for Debugging**
   ```
   kubectl exec -it <pod-name> -n <namespace> -- /bin/sh
   ```
   - Useful for checking **filesystem, environment variables, or dependencies**.  

### 7. **Check Network & DNS Issues**
   ```
   kubectl exec -it <pod-name> -n <namespace> -- nslookup <service-name>
   ```
   - Ensures the **pod can resolve and communicate with other services**.  

### 8. **Restart or Delete the Pod**
   ```
   kubectl delete pod <pod-name> -n <namespace>
   ```
   - If the issue is transient, restarting may resolve it.  
34. How do you document your infrastructure and automation scripts? 
35. How do you use, Ansible Vault to connect secrets?
### 1. **Encrypt a Secret File**
   ```
   ansible-vault encrypt secrets.yml
   ```
   - Stores sensitive data (e.g., passwords, API keys) securely.  

### 2. **Edit an Encrypted File**
   ```
   ansible-vault edit secrets.yml
   ```
   - Allows modification of encrypted secrets.  

### 3. **Use Vault in Playbooks**  
   - Define secrets in `secrets.yml`:
   ```yaml
   db_password: !vault |
         $ANSIBLE_VAULT;1.1;AES256
         encrypted-data-here
   ```
   - Reference in playbooks:
   ```yaml
   - hosts: web
     vars_files:
       - secrets.yml
     tasks:
       - name: Use secret
         debug:
           msg: "DB Password is {{ db_password }}"
   ```

### 4. **Run a Playbook with Vault Password**
   ```
   ansible-playbook playbook.yml --ask-vault-pass
   ```
   - Prompts for the Vault password during execution.  

### 5. **Encrypt a Single Variable**
   ```
   ansible-vault encrypt_string "mypassword" --name "db_password"
   ```
   - Useful for embedding encrypted secrets in playbooks.  

### **Best Practices**
✅ Store Vault password securely (e.g., AWS Secrets Manager).  
✅ Use `ANSIBLE_VAULT_PASSWORD_FILE` for automation.  
✅ Never store unencrypted secrets in Git.

36. how do you store your secrets in AWS secret manager?
### **1. Creating & Storing Secrets**  
- Use **AWS Secrets Manager** to securely store **API keys, database credentials, and sensitive configurations**.  
- Encrypt secrets using **AWS KMS (Key Management Service)**.  
- Store secrets via **AWS Console, CLI, or SDK**:  

  ```sh
  aws secretsmanager create-secret --name MyDBSecret \
    --secret-string '{"username":"admin","password":"mypassword"}'
  ```

### **2. Retrieving Secrets Securely**  
- Access secrets using **AWS SDK, AWS CLI, or AWS Lambda**.  
- Example: Fetch secret in an application:  

  ```python
  import boto3
  client = boto3.client('secretsmanager')
  response = client.get_secret_value(SecretId='MyDBSecret')
  secret = response['SecretString']
  ```

### **3. Best Practices**  
- **Rotate secrets automatically** using AWS **Lambda rotation policies**.  
- Use **IAM policies** to restrict access to secrets.  
- Enable **CloudWatch logging** for monitoring secret access.  
- Use **AWS Parameter Store** for less sensitive configs.
  
38. how do you stay updated with the new dev tools and technologies, and how do you decide which ones to integrate into your workflows?
39. Can you explain a situation where you had to lead a project or an initiative within a DevOps team?
40. what is the difference between a deployment daemonset and statefulset in Kubernetes?
## Difference Between Deployment, DaemonSet, and StatefulSet in Kubernetes  

| Feature         | Deployment | DaemonSet | StatefulSet |
|---------------|------------|------------|------------|
| **Purpose**  | Manages stateless applications | Ensures a pod runs on every node | Manages stateful applications |
| **Scaling**  | Scales up/down dynamically | Runs one pod per node (no scaling) | Scales with stable network identity |
| **Pod Identity** | Pods are replaceable, no fixed identity | No fixed pod identity | Each pod has a unique, stable identity |
| **Use Case** | Web apps, APIs, microservices | Logging, monitoring, security agents | Databases, Kafka, Zookeeper |
| **Pod Replacement** | New pod gets a random name | New pod replaces the old one | New pod retains the same name & storage |
| **Storage** | Uses ephemeral or shared storage | Generally does not require persistent storage | Uses persistent storage (PVC) |

**Each serves a specific use case:**
- **Deployment** for **stateless apps**  
- **DaemonSet** for **node-level agents**  
- **StatefulSet** for **stateful apps requiring stable storage & networking** 🚀  

42. Can you share your screen and write the manifest file Deployment?
Below is a **basic Deployment YAML** file for a **Nginx application** with **3 replicas**:  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```
44. how do you configure a Jenkins pipeline for a containerized application?
45. how do you manage a state in Terraform?
46. What are the Terraform modules, sir, and why are they used?
## Terraform Modules and Their Purpose  

### **What Are Terraform Modules?**  
Terraform **modules** are reusable, self-contained configurations that simplify **infrastructure management** by organizing resources logically.  

### **Types of Terraform Modules**  

| Module Type    | Purpose |
|---------------|---------|
| **Root Module** | The main module where Terraform execution starts (contains `main.tf`). |
| **Child Module** | Reusable submodules called within the root module (`module "network"`). |
| **Public Module** | Pre-built modules available in the Terraform Registry for common use cases. |
| **Private Module** | Custom-built modules stored in a private registry or Git repository. |

### **Why Use Terraform Modules?**  
✅ **Reusability** – Write once, use multiple times across environments.  
✅ **Scalability** – Manage complex infrastructure efficiently.  
✅ **Consistency** – Standardizes configurations across teams.  
✅ **Easier Maintenance** – Updates and bug fixes are easier with modular code.  

### **Example of a Terraform Module Usage**
```hcl
module "network" {
  source  = "./network-module"
  vpc_cidr = "10.0.0.0/16"
}
```
48. What files you mention in the modules?
### **1. Terraform Modules (IaC)**
- **`main.tf`** → Defines resources and configurations.  
- **`variables.tf`** → Declares input variables.  
- **`outputs.tf`** → Defines output values.  
- **`providers.tf`** → Specifies cloud providers (AWS, GCP, etc.).  
- **`terraform.tfvars`** → Stores variable values.  

### **2. Kubernetes Helm Chart Modules**
- **`Chart.yaml`** → Metadata about the Helm chart.  
- **`values.yaml`** → Configurable parameters for deployments.  
- **`templates/`** → Contains Kubernetes manifests (Deployment, Service, Ingress, etc.). 
49. What is,an Ansible playbook and how it is structured?
  ## **Ansible Playbook Structure**  

```yaml
- name: Deploy Nginx Server
  hosts: webservers
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx Service
      service:
        name: nginx
        state: started
        enabled: yes
```
50. How do you set up alerting in a Prometheus?
51. Can you explain how Grafana integrates with Prometheus, for visualization?
52. Do you have any experience in bash script?
53. What what about Python?
54. Jenkins pipeline is stuck on a particular stage. How do you debug it?
55. Your terraform apply command is failing. Okay. What are the steps to prepare for troubleshooting?
## Steps to Troubleshoot `terraform apply` Failure
If `terraform apply` fails, follow these steps:  

### **1. Check the Error Message**  
- Run: `terraform apply -auto-approve` and check logs for **syntax errors, missing variables, or provider issues**.  

### **2. Validate and Format Code**  
- Run: `terraform validate` → Checks for syntax errors.  
- Run: `terraform fmt` → Fixes formatting issues.  

### **3. Check Terraform State & Lock Issues**  
- Run: `terraform state list` → Verify expected resources.  
- If locked, unlock manually: `terraform force-unlock <LOCK_ID>`.  

### **4. Verify Provider & Credentials**  
- Run: `terraform providers` → Ensure required providers are installed.  
- Check **AWS/GCP/Azure credentials**:  
  ```sh
  aws sts get-caller-identity
  ```
### **5. Debug with Detailed Logs**  
- Enable debugging:  
  ```sh
  TF_LOG=DEBUG terraform apply
  ```

### **6. Destroy and Reapply (Last Resort)**  
- Run: `terraform destroy` to clean up resources and retry apply.
  
56. An EC2 instance is unable to connect to an RDS database. What could be the issue?
57. How you will update the version in RDS? In terraform.
## Updating RDS Version in Terraform  

To update the **RDS engine version** in Terraform, follow these steps:  

### **1. Modify the Terraform Configuration**  
Update the `engine_version` in your Terraform RDS resource:  

```hcl
resource "aws_db_instance" "mydb" {
  engine            = "postgres"
  engine_version    = "15.3"  # Update to the desired version
  instance_class    = "db.t3.medium"
  allocated_storage = 20
  apply_immediately = true  # Apply changes immediately (optional)
}
```

### **2. Plan the Changes**  
Run:  
```sh
terraform plan
```
✅ This will show the **proposed changes** to the RDS instance.  

### **3. Apply the Changes**  
Run:  
```sh
terraform apply -auto-approve
```
✅ This will **upgrade the RDS version**.  

### **4. Handle Downtime (If Needed)**  
- If `apply_immediately = true`, the update happens **immediately** (may cause downtime).  
- If `apply_immediately = false`, Terraform schedules the upgrade **during the next maintenance window**.  

### **5. Verify the Update**  
Run:  
```sh
aws rds describe-db-instances --db-instance-identifier mydb
```
✅ Confirms the **new RDS version**.  
59. how you handle the situation where the deployment fails in production?
60. What about the Bluegreen deployment?Have you ever come up with it? how we switch the traffic?
61. Can you explain the role of Docker in your CICD pipelines? Like, how do you ensure the security of containers in your workflow?
62. Can you provide an example where you identified and fixed the security vulnerability using these tools within the pipeline?
63. Can you expand your experience with the AWS services.
64. What do you know about API Gateway?
65. What all AWS services you have worked on.
58) Write a Terraform code to provision EC2 and how will you refer to the EC2 instance id created, write an Ansible playbook for installing python on the EC2 server that is created.
## Terraform Code to Provision an EC2 Instance  

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-0abcdef1234567890"  # Replace with a valid AMI ID
  instance_type = "t2.micro"
  key_name      = "my-key"

  tags = {
    Name = "MyEC2Instance"
  }
}

output "instance_id" {
  value = aws_instance.my_ec2.id
}
```

### **Referencing the EC2 Instance ID in Ansible**
To use the EC2 instance ID in Ansible, output it from Terraform and fetch it dynamically.

---

## **Ansible Playbook to Install Python on EC2**  

```yaml
- name: Install Python on EC2
  hosts: all
  become: yes
  tasks:
    - name: Install Python
      yum:
        name: python3
        state: present
```

### **Steps to Run This:**
1. **Run Terraform:**  
   ```sh
   terraform apply -auto-approve
   ```
2. **Fetch EC2 Public IP:**  
   ```sh
   terraform output instance_id
   ```
3. **Update Ansible Inventory (`inventory.ini`)** with EC2 public IP:  
   ```
   [ec2]
   <EC2_PUBLIC_IP> ansible_user=ec2-user ansible_ssh_private_key_file=~/my-key.pem
   ```
4. **Run Ansible Playbook:**  
   ```sh
   ansible-playbook -i inventory.ini install_python.yml
   ```

This ensures **automated provisioning** of EC2 using Terraform and **configuration management** using Ansible. 🚀  
59) Tell us about Disaster recovery strategies.
## Disaster Recovery (DR) Strategies  

Disaster Recovery (DR) ensures **business continuity** by minimizing downtime and data loss in case of failures.  

### **1. Backup & Restore (Basic DR)**  
- Periodic backups stored in **S3, Glacier, or external storage**.  
- Manual recovery from backups when needed.  
- **Use Case:** Cost-effective for non-critical workloads.  

### **2. Pilot Light (Minimal Standby)**  
- A **scaled-down replica** of the production environment is always running.  
- Quickly scale up when needed.  
- **Use Case:** Databases and core applications.  

### **3. Warm Standby (Partial Failover)**  
- A **smaller, active version** of the full production system is running.  
- Scaled up during a disaster.  
- **Use Case:** Mid-tier applications needing quick recovery.  

### **4. Multi-Site Active-Active (Zero Downtime)**  
- Full workloads running in **multiple regions or data centers**.  
- Traffic is distributed via **Route 53, ALB, or Global Accelerator**.  
- **Use Case:** Mission-critical applications requiring **high availability**.  

### **Best Practices for DR:**  
✅ **Automated Backups** → Use AWS Backup, RDS snapshots.  
✅ **Infrastructure as Code (IaC)** → Terraform for quick environment recreation.  
✅ **Failover Testing** → Regular DR drills to validate recovery plans.  
✅ **Monitoring & Alerts** → CloudWatch, ELK, and Prometheus for early issue detection.  

Choosing the right DR strategy depends on **RTO (Recovery Time Objective) & RPO (Recovery Point Objective)** requirements. 🚀  
## RTO vs. RPO in Disaster Recovery 

### **1. Recovery Time Objective (RTO)**
- **Definition:** Maximum acceptable **downtime** after a failure before services must be restored.  
- **Example:** If RTO = **1 hour**, systems must be back online within 1 hour.  
- **Use Case:** Defines how quickly a business can recover.  

### **2. Recovery Point Objective (RPO)**
- **Definition:** Maximum acceptable **data loss** measured in time before a failure.  
- **Example:** If RPO = **30 minutes**, backups must ensure no data loss beyond 30 minutes.  
- **Use Case:** Defines how frequently backups should occur.  

| **Metric** | **RTO** | **RPO** |
|-----------|--------|--------|
| **Focus** | Downtime | Data Loss |
| **Measures** | Time to recover | Time since last backup |
| **Example** | Restore services within 1 hour | Restore data from last 30-minute backup |

### **Key Considerations**
✅ **Low RTO & RPO** → Requires high-availability, multi-region setups.  
✅ **High RTO & RPO** → Cost-effective, suitable for non-critical apps.  
✅ **Balanced DR Plan** → Depends on business needs & budget.  

Understanding RTO & RPO helps in **choosing the right DR strategy** to minimize downtime and data loss. 🚀  

60) What all Kubernetes services you have used 
61) What all monitoring tools have you used.
62) Write a bash script to read the last 10 lines of a file and how to get lines having word "out of memory''.
    ### **1. Read the Last 10 Lines of a File**
```bash
#!/bin/bash
file="application.log"  # Replace with your file

# Display last 10 lines and display out of memory
tail -n 10 "$file" | grep -i "out of memory"
```
64) How service mesh is used.
65) How have you optimized codes.
66) What security tools are you using.
67) If you have exhausted all ip addresses of your vpc,what will you do
## Steps to Handle VPC IP Exhaustion  

1. **Check Available IPs**  
   ```sh
   aws ec2 describe-subnets --query "Subnets[*].{ID:SubnetId, AvailableIPs:AvailableIpAddressCount}"
   ```

2. **Release Unused IPs**  
   - Delete unused **ENIs, EC2 instances, and NAT Gateways**.  

3. **Add a New Subnet** (If CIDR Allows)  
   ```sh
   aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.0.2.0/24
   ```

4. **Associate a Secondary CIDR Block**  
   ```sh
   aws ec2 associate-vpc-cidr-block --vpc-id vpc-12345678 --cidr-block 10.1.0.0/16
   ```

5. **Optimize IP Usage**  
   - Use **PrivateLink, ALB, and IPv6** where possible.  

69) how will you create a vpc for enviroment in aws and the usage of ip address whould be limited

## Creating a VPC with Limited IP Usage in AWS  

### **1. Create a VPC with a Defined CIDR Block**  
```hcl
resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/24"  # Smaller CIDR to limit IP allocation
  enable_dns_support = true
  enable_dns_hostnames = true
  tags = {
    Name = "Limited-IP-VPC"
  }
}
```
✅ **Limits IP usage** by restricting the subnet size.  

---

### **2. Create a Subnet with Limited IPs**  
```hcl
resource "aws_subnet" "my_subnet" {
  vpc_id            = aws_vpc.my_vpc.id
  cidr_block        = "10.0.0.0/26"  # Only 64 IPs available
  map_public_ip_on_launch = false
  tags = {
    Name = "Limited-IP-Subnet"
  }
}
```
✅ Smaller subnets control IP allocation.

---

### **3. Restrict Unnecessary IP Consumption**  
- Use **PrivateLink** instead of NAT Gateway to reduce IP consumption.  
- Deploy **ECS with Fargate** (no ENI overhead).  
- Assign **Elastic IPs only when necessary**.  

---

### **4. Monitor & Optimize IP Usage**  
Run:  
```sh
aws ec2 describe-subnets --query "Subnets[*].{ID:SubnetId, AvailableIPs:AvailableIpAddressCount}"
```
✅ **Regular monitoring** ensures optimal usage.

1) .bashrc file.
The `. bashrc file` is a configuration file for the Bash shell. The file consists of commands, functions, aliases, and scripts that execute every time a Bash session starts on Linux or macOS
1) Run levels in Linux
2) Namespaces in linux
   Namespaces in Linux are a kernel feature that isolate processes from each other by limiting the resources they can use 
  **Network namespace**: Isolates network interfaces, routing tables, and firewall rules 
- **User namespace**: Isolates user accounts running processes 
- **Mount namespace**: Isolates mount points so that processes in different namespaces can't see each other's files
- **PID namespace**: Separates processes so that processes in the child namespace can't know about the parent process
- **IPC namespace**: Isolates process communication mechanisms like semaphores, message queues, and shared memory segments
1) What happens first when Lyinux server is started.
   Refer to this doc 
   https://www.freecodecamp.org/news/the-linux-booting-process-6-steps-described-in-detail/
2) What are daemon services.
   A **daemon service** is a computer program that runs continuously in the background of an operating system, performing various tasks without direct user interaction
3) How will you mount a storage to a filesystem.
`lsblk`
`sudo mkdir -p /mnt/storage`
`sudo mount dev/sda2 /mnt/storage`
1) What are Git semantics.
   Git semantics refer to conventions and practices that add meaningful context to Git operations, enhancing collaboration and automation. Key aspects include
   `<type>[optional scope]: <description>`
For reference use this below doc:
https://dev.to/ghostaram/writing-meaningful-git-commit-messages-with-semantic-tags-1dim

1) How will you resolve merge conflicts
2) What branching strategies you follow.
3) Write Pipeline Script
4) How will you trigger a pipeline for Dev , QA and prod
https://www.youtube.com/watch?v=WUra9ugnVhs&t=80s
5) what is multibranch pipeline
6) Write docker file to install python
```
# Use an official Python base image
FROM python:3.11-slim

# Set the working directory inside the container
WORKDIR /app

# Copy application files to the container
COPY . .

# Install dependencies (if a requirements.txt file exists)
RUN pip install --no-cache-dir -r requirements.txt

# Set the default command to run Python
CMD ["python"]
```

7) Write multistage docker file to deploy the small application in java
```
# Stage 1: Build Stage
FROM maven:3.9.4-eclipse-temurin-17 AS build

# Set working directory
WORKDIR /app

# Copy Maven project files
COPY pom.xml .
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests

# Stage 2: Runtime Stage
FROM eclipse-temurin:17-jre

# Set working directory
WORKDIR /app

# Copy the built JAR file from the build stage
COPY --from=build /app/target/*.jar app.jar

# Expose application port (modify as needed)
EXPOSE 8080

# Command to run the application
CMD ["java", "-jar", "app.jar"]
```
8) difference between COPY and ADD
| Feature        | COPY                          | ADD                          |
|--------------|-----------------------------|-----------------------------|
| **Basic Function** | Copies files/directories from source to destination | Copies files like `COPY`, but also supports additional features |
| **Remote URL Support** | ❌ No, cannot fetch files from URLs | ❌ No, does not support remote URLs |
| **Extracts Archives** | ❌ No, copies `.tar`, `.zip` as is | ✅ Yes, automatically extracts `.tar` files (but not `.zip`) |
| **Local Files & Directories** | ✅ Yes | ✅ Yes |
| **Recommended Use** | When only copying files/directories | When automatic extraction of `.tar` files is needed |
| **Security Consideration** | Safer, as it does only copying | Less secure due to implicit extraction behavior |
| **Syntax** | `COPY <source> <destination>` | `ADD <source> <destination>` |

9) difference between CMD and ENTRYPOINT
10) How to know which port container is running.
11) What is the flow when kubectl apply -f manifest.yml runs.
12) What is ingress, what kind of routing it provides.
13) what does kube-proxy helps in.
14) What does kubelet do.
## **📌 Overview**
`kubelet` is a critical component of Kubernetes that runs on **each node** in the cluster. It ensures that the containers in a **Pod** are running and remain in the desired state.

## **🔹 Key Responsibilities**
| Feature | Description |
|---------|------------|
| **Pod Management** | Ensures that Pods are running as defined in the PodSpec. |
| **Container Lifecycle** | Starts, stops, and restarts containers as needed. |
| **Health Checks** | Monitors container health via **liveness** and **readiness probes**. |
| **Communicates with API Server** | Receives instructions from the Kubernetes API Server. |
| **Logs & Metrics Collection** | Integrates with tools like Prometheus for monitoring. |
| **Works with Container Runtime** | Uses Docker, containerd, or CRI-O to manage containers. |
| **Syncs Node Status** | Updates the API server about node status (CPU, memory, etc.). |

## **🔹 How Kubelet Works**
1. **Receives Pod Specifications** from the API Server.
2. **Interacts with the Container Runtime** (Docker, containerd) to start/stop containers.
3. **Performs health checks** on Pods and restarts them if they fail.
4. **Reports node status** back to the control plane.

## **🔹 Commands to Check Kubelet Status**
```sh
# Check if kubelet is running
systemctl status kubelet

# Restart kubelet
systemctl restart kubelet

# Check kubelet logs
journalctl -u kubelet -f
```
15) What are default namespaces.
Kubernetes comes with **four default namespaces** that help organize cluster resources.  

## **📌 List of Default Namespaces**
| Namespace | Description |
|-----------|------------|
| **default** | Used for resources that don't specify a namespace. |
| **kube-system** | Contains system components like the API server, controller manager, scheduler, and kube-proxy. |
| **kube-public** | Readable by everyone, typically used for publicly accessible data. |
| **kube-node-lease** | Stores node heartbeat leases to improve node failure detection. |

---

## **🔹 Details of Each Namespace**

### **1️⃣ default**
- When you create a Pod **without specifying a namespace**, it is placed in `default`.  
- Example:
```
  kubectl get pods --namespace=default
```


16) In which services Cluster IP service will be used.
ClusterIP is the **default** service type in Kubernetes. It is used for **internal communication** between Pods **inside** the cluster.
17) What are subnets,  how do you know that it is private or public subnet
18) What is VPC peering.
VPC peering is a networking connection that allows two or more Virtual Private Clouds (VPCs) to communicate with each other
https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html#:~:text=A%20VPC%20peering%20connection%20is,are%20within%20the%20same%20network.
19) How will you expose private subnet servers to internet.
20) how to unlock the state file when it is locked in remote backend
     `terraform force-unlock <lock_id>`
21) write a multistage DockerFile for java
22) how will you save the credentials and how will you refer in the jenkins pipeline
23) how to you will connect the 2 gitlabs which is hosted on the 2 regions
24) how will you run the single jenkins pipeline in multiple enviroment  
25) what is gated checkedin 
26) where will save the passwords in aws and how do you call in pipelines
27) what is SBOM tool
28) Command to list down all the remote repository branches.
29) Difference between branch and tag.
30) Difference between Git fetch and Git clone.
31) Command to revert changes from staging area to working area.
32) What does Git revert command do.
33) How can we change default port number of Jenkins.
34) What are sticky sessions.
35) What is a difference between Poll SCM and Build Periodically.
36) How do you maintain backup in Jenkins.
37) What are views in Jenkins. How do we create views.
38) what is the use of terraform refresh 
- **`terraform apply -refresh-only`**: This updates the state without making any changes to the infrastructure.
- **`terraform plan -refresh-only`**: This shows what would be updated in the state without applying changes.
    - **Detect Drift**: If changes were made manually to cloud resources, `terraform refresh` would update the state file.
	- **Update Outputs**: It ensured that output values reflected the current infrastructure.
	- **Prepare for Planning**: It helped in syncing Terraform state before running `terraform plan`.


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
