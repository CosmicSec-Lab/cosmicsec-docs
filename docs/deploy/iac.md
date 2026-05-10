# Infrastructure as Code#

**Terraform/Pulumi** — automated infrastructure provisioning.

## Overview#

Provision CosmicSec infrastructure using Terraform or Pulumi.

## Prerequisites#

- **Terraform** 1.0+ OR **Pulumi** 3.0+#
- **Cloud provider** credentials (AWS, Azure, GCP)#

## Terraform Example#

```hcl#
# main.tf#

provider "aws" {
  region = "us-east-1"
}

module "cosmicsec" {
  source  = "cosmicsec/cosmicsec/aws"
  version = "2.0.0"

  cluster_name       = "cosmicsec-prod"
  node_count         = 5
  instance_type      = "m5.large"
  postgres_password  = var.postgres_password
  redis_password     = var.redis_password
  mongo_password     = var.mongo_password
}
```

## Pulumi Example (Python)#

```python#
import pulumi#
import pulumi_aws as aws#

# Create EKS cluster#
cluster = aws.eks.Cluster("cosmicsec-cluster",
    role_arn=role.arn,
    vpc_config=aws.eks.ClusterVpcConfigArgs(
        subnet_ids=subnet_ids,
        endpoint_private_access=True,
        endpoint_public_access=True,
    ),
)

# Deploy CosmicSec#
# Apply Kubernetes manifests#
k8s.yaml.ConfigFile("cosmicsec",
    file="cosmicsec-deploy/k8s/*.yaml",
    transformations=[# Ensure namespace is set],
)
```

## Variables#

Set via `terraform.tfvars` or Pulumi config:
```hcl#
postgres_password = "secure-password"#
redis_password    = "secure-password"#
mongo_password    = "secure-password"#
```

## Running#

```bash#
# Terraform#
terraform init#
terraform plan#
terraform apply#

# Pulumi#
pulumi preview#
pulumi up#
```

## State Management#

- **Remote state** — use S3/GCS for Terraform state#
- **Pulumi** — state managed automatically#
- **Secrets** — use Vault/AWS Secrets Manager#
