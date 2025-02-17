# devops-interview
interview questions for devops

1. What scripting languages are you familiar with?
2. What are artifacts in GitLab CI?
3. What is a private module registry in Terraform?
4. If you delete the local Terraform state file and it's not stored in S3 or DynamoDB, how can you recover it?
5. How do you import resources into Terraform?
6. What is a dynamic block in Terraform?
7. How can you create EC2 instances in two different AWS accounts simultaneously using Terraform?
8. How do you handle an error stating that the resource already exists when creating resources with Terraform?
9. How does Terraform refresh work?
10. How would you upgrade Terraform plugins?
11. What are the different types of Kubernetes volumes?
12. If a pod is in a crash loop, what might be the reasons, and how can you recover it?
13. What is the difference between StatefulSet and DaemonSet?
14. What is a sidecar container in Kubernetes, and what are its use cases?
15. If pods fail to start during a rolling update, what strategy would you use to identify the issue and rollback?
16. How can we enable communication between 500 AWS accounts internally?
17. How to configure a solution where a Lambda function triggers on an S3 upload and updates DynamoDB?
18. What is the standard port for RDP?
19. How do you configure a Windows EC2 instance to join an Active Directory domain?
20. How can you copy files from a Linux server to an S3 bucket?
21. What permissions do you need to grant for that S3 bucket?
22. What are the different types of VPC endpoints and when do you use them?
23. How to resolve an image pullback error when using an Alpine image pushed to ECR in a pipeline?
24. What is the maximum size of an S3 object?
25. What encryption options do we have in S3?
26. Can you explain IAM user, IAM role, and IAM group in AWS?
27. What is the difference between an IAM role and an IAM policy document?
28. What are inline policies and managed policies?
29. How can we add a load balancer to Route 53?
30. What are A records and CNAME records?
31. What is the use of a target group in a load balancer?
32. If a target group is unhealthy, what might be the reasons?
33. Can you share your screen and write a Jenkins pipeline?
34. How do you write parallel jobs in a Jenkins pipeline?


# terraform commands and functionality 

1. terraform init: Initializes a Terraform working directory.
2. terraform validate: Validates the Terraform configuration files.
3. terraform fmt: Formats the Terraform configuration files.

▶️ Section 2: Infrastructure Management
4. terraform apply: Applies the configuration to create or update infrastructure.
5. terraform destroy: Destroys the infrastructure managed by Terraform.
6. terraform refresh: Refreshes the Terraform state to match the actual infrastructure.
7. terraform show: Shows the Terraform state and configuration.

▶️ Section 3: State Management
8. terraform state list: Lists the resources in the Terraform state.
9. terraform state show: Shows the details of a specific resource in the Terraform state.
10. terraform state rm: Removes a resource from the Terraform state.
11. terraform state mv: Moves a resource from one state to another.

▶️ Section 4: Module Management
12. terraform get: Downloads and installs Terraform modules.
13. terraform module: Manages Terraform modules.
14. terraform module init: Initializes a Terraform module.

▶️ Section 5: Provider Management
1. terraform providers: Lists the available Terraform providers.
2. terraform provider: Manages Terraform providers.
3. terraform provider init: Initializes a Terraform provider.

▶️ Section 6: Workspace Management
4. terraform workspace: Manages Terraform workspaces.
5. terraform workspace new: Creates a new Terraform workspace.
6. terraform workspace select: Selects a Terraform workspace.

▶️ Section 7: Debugging and Troubleshooting
7. terraform debug: Enables debug logging for Terraform.
8. terraform logs: Shows the Terraform logs.
9. terraform console: Opens a Terraform console for interactive debugging.

▶️ Section 8: Import and Export
10. terraform import: Imports existing infrastructure into Terraform.
11. terraform export: Exports the Terraform state to a file.

▶️ Section 9: Miscellaneous
12. terraform version: Shows the Terraform version.
13. terraform help: Shows the Terraform help.
14. terraform upgrade: Upgrades Terraform to the latest version.

▶️ Section 10: Advanced Topics
15. terraform console: Opens a Terraform console for interactive debugging.
16. terraform graph: Generates a graph of the Terraform configuration.
17. terraform output: Shows the output of a Terraform configuration.

▶️ Section 11: Terraform CLI
18. terraform cli: Manages the Terraform CLI.
19. terraform cli config: Configures the Terraform CLI.

▶️ Section 12: Terraform Configuration
20. terraform config: Manages the Terraform configuration.
21. terraform config init: Initializes a Terraform configuration.

▶️ Section 13: Terraform State Backend
22. terraform state backend: Manages the Terraform state backend.
23. terraform state backend init: Initializes a Terraform state backend.

▶️ Section 14: Terraform Workspaces
24. terraform workspace: Manages Terraform workspaces.
25. terraform workspace new: Creates a new Terraform
# CI/CD

🚨 CI/CD ♾ real-time related Question & Answer:-


26. What is a CI/CD pipeline?
A CI/CD pipeline is an automated workflow that integrates and delivers code changes continuously. It consists of processes like code integration, building, testing, deployment, and delivery. The goal of CI/CD pipelines is to deliver software updates quickly, reliably, and consistently, reducing the risk of errors and improving collaboration.

27. How do you implement a CI/CD pipeline from scratch?
Version Control: Start by ensuring the code is managed in a version control system (e.g., Git).
Build Automation: Set up build automation tools (e.g., Jenkins, GitLab CI/CD, GitHub Actions) that will compile and package your code.
Testing: Integrate automated testing for unit, integration, and acceptance tests.
Artifact Repository: Use an artifact repository (e.g., Nexus, Artifactory) for storing build artifacts.
Deployment Automation: Automate the deployment process using tools like Ansible, Docker, or Kubernetes.
Monitoring and Alerts: Set up monitoring tools to alert about issues post-deployment.

28. What are the common stages of a CI/CD pipeline?
Source Code Control: Code commits trigger the pipeline.
Build: The code is compiled and packaged.
Test: Automated testing, including unit, integration, and functional tests, are run.
Release/Deploy: The code is deployed to staging or production environments.

29. How do you manage secrets in a CI/CD pipeline?
Using secret management tools (e.g., HashiCorp Vault, AWS Secrets Manager, Azure Key Vault).
Storing secrets in environment variables or vaults outside the codebase.
Using pipeline tools’ native secret management features (e.g., Jenkins credentials store).

30. Explain the importance of automated testing in CI/CD?
Automated testing ensures code quality by catching issues early in the pipeline, preventing faulty code from reaching production. It helps:
Maintain code consistency. Reduce human error. Accelerate feedback loops, allowing developers to fix issues faster.
Ensure that changes don’t introduce regressions.

31. How do you ensure that deployments are zero-downtime?
Use blue-green deployments or canary releases to gradually roll out new versions while keeping the old version live.
Leverage container orchestration platforms like Kubernetes, which can manage rolling updates. Ensure that the database schema and application logic are backward-compatible during updates.
Implement load balancers to route traffic between old and new versions.

32. How do you handle rollbacks in CI/CD?
Versioning Artifacts: Store previous builds and redeploy an older version in case of failure.
Blue-Green Deployments: Switch back to the old version if the new version fails.
Database Migrations: Use reversible migrations to ensure that changes can be rolled back easily.
Monitoring and Alerts: Integrate automated rollback triggers based on predefined metrics or errors.

# git commands

33. 𝗴𝗶𝘁 𝗶𝗻𝗶𝘁: Initializes a new Git repository in the current directory.
34. 𝗴𝗶𝘁 𝗰𝗹𝗼𝗻𝗲 [𝘂𝗿𝗹]: Clones a repository into a new directory.
35. 𝗴𝗶𝘁 𝗮𝗱𝗱 [𝗳𝗶𝗹𝗲]: Adds a file or changes in a file to the staging area.
36. 𝗴𝗶𝘁 𝗰𝗼𝗺𝗺𝗶𝘁 -𝗺 "[𝗺𝗲𝘀𝘀𝗮𝗴𝗲]": Records changes to the repository with a descriptive message.
37. 𝗴𝗶𝘁 𝗽𝘂𝘀𝗵: Uploads local repository content to a remote repository.
38. 𝗴𝗶𝘁 𝗽𝘂𝗹𝗹: Fetches changes from the remote repository and merges them into the local branch.
39. 𝗴𝗶𝘁 𝘀𝘁𝗮𝘁𝘂𝘀: Displays the status of the working directory and staging area.
40. 𝗴𝗶𝘁 𝗯𝗿𝗮𝗻𝗰𝗵: Lists all local branches in the current repository.
41. 𝗴𝗶𝘁 𝗰𝗵𝗲𝗰𝗸𝗼𝘂𝘁 [𝗯𝗿𝗮𝗻𝗰𝗵]: Switches to the specified branch.
42. 𝗴𝗶𝘁 𝗺𝗲𝗿𝗴𝗲 [𝗯𝗿𝗮𝗻𝗰𝗵]: Merges the specified branch's history into the current branch.
43. 𝗴𝗶𝘁 𝗿𝗲𝗺𝗼𝘁𝗲 -𝘃: Lists the remote repositories along with their URLs.
44. 𝗴𝗶𝘁 𝗹𝗼𝗴: Displays commit logs.
45. 𝗴𝗶𝘁 𝗿𝗲𝘀𝗲𝘁 [𝗳𝗶𝗹𝗲]: Unstages the file, but preserves its contents.
46. 𝗴𝗶𝘁 𝗿𝗺 [𝗳𝗶𝗹𝗲]: Deletes the file from the working directory and stages the deletion.
47. 𝗴𝗶𝘁 𝘀𝘁𝗮𝘀𝗵: Temporarily shelves (or stashes) changes that haven't been committed.
48. 𝗴𝗶𝘁 𝘁𝗮𝗴 [𝘁𝗮𝗴𝗻𝗮𝗺𝗲]: Creates a lightweight tag pointing to the current commit.
49. 𝗴𝗶𝘁 𝗳𝗲𝘁𝗰𝗵 [𝗿𝗲𝗺𝗼𝘁𝗲]: Downloads objects and refs from another repository.
50. 𝗴𝗶𝘁 𝗺𝗲𝗿𝗴𝗲 --𝗮𝗯𝗼𝗿𝘁: Aborts the current conflict resolution process, and tries to reconstruct the pre-merge state.
51. 𝗴𝗶𝘁 𝗿𝗲𝗯𝗮𝘀𝗲 [𝗯𝗿𝗮𝗻𝗰𝗵]: Reapplies commits on top of another base tip, often used to integrate changes from one branch onto another cleanly.
52. 𝗴𝗶𝘁 𝗰𝗼𝗻𝗳𝗶𝗴 --𝗴𝗹𝗼𝗯𝗮𝗹 𝘂𝘀𝗲𝗿.𝗻𝗮𝗺𝗲 "[𝗻𝗮𝗺𝗲]" 𝗮𝗻𝗱 𝗴𝗶𝘁 𝗰𝗼𝗻𝗳𝗶𝗴 --𝗴𝗹𝗼𝗯𝗮𝗹 𝘂𝘀𝗲𝗿.𝗲𝗺𝗮𝗶𝗹 "[𝗲𝗺𝗮𝗶𝗹]": Sets the name and email to be used with your commits.
53. 𝗴𝗶𝘁 𝗱𝗶𝗳𝗳: Shows changes between commits, commit and working tree, etc.
54. 𝗴𝗶𝘁 𝗿𝗲𝗺𝗼𝘁𝗲 𝗮𝗱𝗱 [𝗻𝗮𝗺𝗲] [𝘂𝗿𝗹]: Adds a new remote repository.
55. 𝗴𝗶𝘁 𝗿𝗲𝗺𝗼𝘁𝗲 𝗿𝗲𝗺𝗼𝘃𝗲 [𝗻𝗮𝗺𝗲]: Removes a remote repository.
56. 𝗴𝗶𝘁 𝗰𝗵𝗲𝗰𝗸𝗼𝘂𝘁 -𝗯 [𝗯𝗿𝗮𝗻𝗰𝗵]: Creates a new branch and switches to it.
57. 𝗴𝗶𝘁 𝗯𝗿𝗮𝗻𝗰𝗵 -𝗱 [𝗯𝗿𝗮𝗻𝗰𝗵]: Deletes the specified branch.
58. 𝗴𝗶𝘁 𝗽𝘂𝘀𝗵 --𝘁𝗮𝗴𝘀: Pushes all tags to the remote repository.
59. 𝗴𝗶𝘁 𝗰𝗵𝗲𝗿𝗿𝘆-𝗽𝗶𝗰𝗸 [𝗰𝗼𝗺𝗺𝗶𝘁]: Picks a commit from another branch and applies it to the current branch.
60. 𝗴𝗶𝘁 𝗳𝗲𝘁𝗰𝗵 --𝗽𝗿𝘂𝗻𝗲: Prunes remote tracking branches no longer on the remote.
61. 𝗴𝗶𝘁 𝗰𝗹𝗲𝗮𝗻 -𝗱𝗳: Removes untracked files and directories from the working directory.
62. 𝗴𝗶𝘁 𝘀𝘂𝗯𝗺𝗼𝗱𝘂𝗹𝗲 𝘂𝗽𝗱𝗮𝘁𝗲 --𝗶𝗻𝗶𝘁 --𝗿𝗲𝗰𝘂𝗿𝘀𝗶𝘃𝗲: Initializes and updates submodules recursively.


Exp: 3yrs
Role: DevOps consultant 
Time: 45min

63. Walk me through ur profile?
64. What are the tools & technologies you have worked on ?
3.Tell me about traditional devops vs gitops approach?
65. How does Argocd works?
66. What are all the best practices you 've followed to identify early bugs/issues analysis?
67. Walk me through your pipeline in your projects?
68. What is docker file and what it contains?
69. What is layer in docker file?
70. Diff between cmd vs entrypoint ?
71. What do you use for image scanning ?
72. How do you measures code quality analysis and what were quality gate checks you 've applied?
73. What are DevSecOps practices you 've applied inorder to secure your systems?
74. When you are using git as scm and ci/cd on Jenkins, what is best way of integrating with github?
75. How the images you pushed in private docker hub taken by k8s?
76. What do you mean by infrastructure provisioning?
77. What are the resources you created using Terraform?
78. What are essentials(softwares,tools) you need in terraform to create resources on aws cloud?
79. What is the architecture of ansible and why  someone need to use it?
80. What are the activities you have done using ansible?
81. What is ansible playbook ?
82. What are best metrics or practice you take into consideration in broader aspect as a DevSecOps Engineer?
83. What are the metrics &  considerations in high level you take inorder to update to a stakeholders?
84. How does prometheus scrape metrics of a new service application  updates ?
85. How does Grafana works ?
86. How does Loki works ?
87. Explain the deployment strategies you have worked upon?
88. Explain the consequences of blue-green deployment?
89. How 'll you manage the lambda function to can get the secrets from secret manager? 