## AWS EKS Autoscaling of a 3-tier architecture website using Terraform:

### Prerequisites:
1. AWS Account: Ensure you have an AWS account with appropriate permissions to create resources like EKS clusters, EC2 instances, IAM roles, etc.
2. Terraform Installed: Make sure Terraform is installed on your local machine or on the CI/CD server.
3. IAM Permissions: Ensure IAM user or role used by Terraform has necessary permissions to create and manage resources.
4. AWS CLI: Install AWS CLI and configure it with appropriate access credentials.
5. Kubernetes (kubectl): Install kubectl to interact with the Kubernetes cluster.

### Step 1: Set up Terraform Configuration
1. Create a new directory for your Terraform configuration.
2. Inside the directory, create a `main.tf` file where you'll define your AWS resources.
3. Define your provider in `main.tf`:

```hcl
provider "aws" {
  region = "us-west-2" // Change this to your preferred region
}
```

### Step 2: Provision AWS EKS Cluster
1. Define the EKS cluster configuration in `main.tf`:

```hcl
module "eks" {
  source       = "terraform-aws-modules/eks/aws"
  cluster_name = "my-cluster"
  cluster_version = "1.21"
  subnets      = ["subnet-xxxxxxxxxxxxxxx", "subnet-yyyyyyyyyyyyyyy"] // Specify your subnets
  vpc_id       = "vpc-xxxxxxxxxxxxxxxxx" // Specify your VPC ID
}
```

2. Initialize Terraform:

```bash
terraform init
```

3. Apply the configuration to create the EKS cluster:

```bash
terraform apply
```

### Step 3: Configure Autoscaling
1. Define the autoscaling group for worker nodes:

```hcl
module "autoscaling" {
  source             = "terraform-aws-modules/autoscaling/aws"
  version            = "~> 4.0"
  name               = "my-node-group"
  launch_configuration_name = aws_launch_configuration.worker.name
  min_size           = 1
  max_size           = 10
  desired_capacity   = 2
}
```

2. Define launch configuration for worker nodes:

```hcl
resource "aws_launch_configuration" "worker" {
  name                 = "my-launch-config"
  image_id             = "ami-xxxxxxxxxxxxxxxxx" // Specify your AMI
  instance_type        = "t2.micro" // Specify your instance type
  security_groups      = [aws_security_group.worker.id] // Specify your security group
  iam_instance_profile = aws_iam_instance_profile.worker.name
  user_data            = file("scripts/worker_userdata.sh") // Define your user data script
}
```

### Step 4: Deploy Application to EKS
1. Deploy your application to EKS using Kubernetes manifests or Helm charts.
2. Use `kubectl` to apply the deployment and service YAML files:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### Step 5: Verify Autoscaling
1. Monitor the EKS cluster and worker nodes using AWS CloudWatch.
2. Load test your application to trigger autoscaling.
3. Monitor the number of worker nodes in the autoscaling group to see if it scales up or down based on the load.

### Step 6: Cleanup
1. When finished, destroy the resources created by Terraform:

```bash
terraform destroy
```

### Conclusion
This documentation provides a detailed guide on setting up AWS EKS autoscaling for a 3-tier architecture website using Terraform. Make sure to customize the configurations according to your specific requirements and best practices.
