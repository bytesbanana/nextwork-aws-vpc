# AWS VPC Terraform Configuration

This Terraform configuration creates a complete VPC infrastructure in AWS with both public and private subnets, including EC2 instances, security groups, and network configurations.

## Infrastructure Components

- **VPC**: A custom VPC with specified CIDR block
- **Subnets**:
  - Public subnet with internet access
  - Private subnet for internal resources
- **Networking**:
  - Internet Gateway for public access
  - Route Tables for both public and private subnets
  - Network ACLs for additional security
- **Security**:
  - Public Security Group for internet-facing resources
  - Private Security Group for internal resources
- **Compute**:
  - Public EC2 instance in the public subnet
  - Private EC2 instance in the private subnet

## Prerequisites

- AWS CLI configured with appropriate credentials
- Terraform installed (version >= 1.0.0)
- SSH key pair for EC2 instances

## Usage

1. Initialize Terraform:
   ```bash
   terraform init
   ```

2. Review the configuration:
   ```bash
   terraform plan
   ```

3. Apply the configuration:
   ```bash
   terraform apply
   ```

## Configuration Variables

The following variables can be customized in `variables.tf` or through the command line:

- `region`: AWS region to deploy resources (default: "ap-southeast-1")
- `vpc_cidr`: CIDR block for VPC (default: "10.0.0.0/16")
- `public_subnet_cidr`: CIDR for public subnet (default: "10.0.0.0/24")
- `private_subnet_cidr`: CIDR for private subnet (default: "10.0.1.0/24")
- `resource_prefix`: Prefix for resource names (default: "NextWork")
- `instance_type`: EC2 instance type (default: "t3.micro")

## Outputs

The configuration provides several useful outputs:

- `vpc_id`: ID of the created VPC
- `public_subnet_id`: ID of the public subnet
- `private_subnet_id`: ID of the private subnet
- `public_instance_public_ip`: Public IP of the EC2 instance in public subnet
- `private_instance_private_ip`: Private IP of the EC2 instance in private subnet
- `public_security_group_id`: ID of the public security group
- `private_security_group_id`: ID of the private security group

## Architecture

```mermaid
graph TB
    %% Define styles
    classDef aws fill:#FF9900,stroke:#232F3E,stroke-width:2px,color:#232F3E
    classDef vpc fill:#F58536,stroke:#232F3E,stroke-width:2px,color:white
    classDef subnet fill:#4391E0,stroke:#232F3E,stroke-width:2px,color:white
    classDef instance fill:#D86613,stroke:#232F3E,stroke-width:2px,color:white
    classDef secgroup fill:#7AA116,stroke:#232F3E,stroke-width:2px,color:white

    %% Internet Gateway
    IG[Internet Gateway]
    
    %% VPC and Subnets
    VPC[VPC: 10.0.0.0/16]
    PublicSubnet[Public Subnet: 10.0.0.0/24]
    PrivateSubnet[Private Subnet: 10.0.1.0/24]
    
    %% EC2 Instances
    PublicEC2[Public EC2 Instance]
    PrivateEC2[Private EC2 Instance]
    
    %% Security Groups
    PublicSG[Public Security Group]
    PrivateSG[Private Security Group]
    
    %% Network Connections
    Internet((Internet))
    
    %% Define relationships
    Internet --> IG
    IG --> PublicSubnet
    PublicSubnet --> PublicEC2
    PublicSubnet --> PrivateSubnet
    PrivateSubnet --> PrivateEC2
    
    %% Security Group connections
    PublicSG --> PublicEC2
    PrivateSG --> PrivateEC2
    
    %% Group everything in VPC
    subgraph VPCNetwork[AWS VPC]
        VPC
        subgraph PublicResources[Public Resources]
            PublicSubnet
            PublicEC2
            PublicSG
        end
        subgraph PrivateResources[Private Resources]
            PrivateSubnet
            PrivateEC2
            PrivateSG
        end
        IG
    end
    
    %% Apply styles
    class IG aws
    class VPC vpc
    class PublicSubnet,PrivateSubnet subnet
    class PublicEC2,PrivateEC2 instance
    class PublicSG,PrivateSG secgroup
```

## Security

- Public subnet allows inbound HTTP traffic (port 80)
- Private subnet instances can only be accessed from the public subnet
- Network ACLs provide additional network security
- Security groups control instance-level access

## Cleanup

To remove all created resources:
```bash
terraform destroy
```

## Notes

- Make sure to have appropriate AWS credentials configured
- Review security group rules before deploying to production
- Costs will be incurred for the created AWS resources
- Consider adding additional security measures for production use

## File Structure

```
.
├── README.md          # This documentation
├── main.tf           # Main Terraform configuration
├── variables.tf      # Variable definitions
└── output.tf         # Output definitions
```