# Configure Dynamic Inventory with Ansible, Terraform, and AWS

## Overview

This project demonstrates the integration of **Terraform**, **Ansible**, and **AWS** to configure a dynamic inventory system. It eliminates the need for hard-coded server addresses in Ansible inventory files by leveraging the AWS EC2 plugin. The solution automates the provisioning of EC2 instances, configuration of Docker, and deployment of a Dockerized application using dynamic inventory.

## Features

- Provision multiple EC2 instances using **Terraform**.
- Dynamically manage inventory using the **Ansible AWS EC2 plugin**.
- Configure Docker and Docker Compose on EC2 instances via Ansible.
- Deploy and manage a Dockerized application with Ansible playbooks.

---

## Technologies Used

- **Terraform**: Infrastructure as Code (IaC) for provisioning AWS resources.
- **Ansible**: Configuration management and application deployment.
- **AWS**: Cloud platform for hosting EC2 instances.

---

## Project Structure

```plaintext
.
├── terraform/
│   ├── main.tf               # Terraform configuration file
│   ├── providers.tf          # Terraform providers configuration
│   ├── terraform.tfvars      # Input variables for Terraform (not checked into version control)
├── inventory_aws_ec2.yaml    # Ansible dynamic inventory plugin configuration
├── ansible.cfg               # Ansible configuration file
├── deploy_use_inventory.yaml # Ansible playbook for Docker setup and deployment
```

---

## Steps to Run the Project

### 1. Provision Infrastructure with Terraform

1. Navigate to the `terraform` directory:
   ```bash
   cd terraform
   ```

2. Initialize Terraform:
   ```bash
   terraform init
   ```

3. Apply the Terraform configuration to create the VPC, subnets, security groups, and EC2 instances:
   ```bash
   terraform apply --auto-approve
   ```

4. Note the public IPs of the created EC2 instances from the output.

---

### 2. Configure Dynamic Inventory with Ansible

1. Install the required Python libraries for the AWS EC2 plugin:
   ```bash
   pip install boto3 botocore
   ```

2. Verify the `inventory_aws_ec2.yaml` configuration file:
   ```yaml
   plugin: aws_ec2
   regions:
     - us-east-1
   filters:
     tag:Name: dev*
     instance-state-name: running
   ```

3. Update `ansible.cfg` with the following settings:
   ```ini
   [defaults]
   inventory = inventory_aws_ec2.yaml
   host_key_checking = False
   enable_plugins = aws_ec2
   remote_user = ec2-user
   private_key_file = ~/.ssh/id_ed22519_ec2
   ```

4. Test the dynamic inventory setup:
   ```bash
   ansible-inventory -i inventory_aws_ec2.yaml --graph
   ```

---

### 3. Deploy and Configure EC2 Instances with Ansible

1. Execute the Ansible playbook:
   ```bash
   ansible-playbook deploy_use_inventory.yaml
   ```

2. The playbook performs the following:
   - Installs Docker on the EC2 instances.
   - Creates a new Linux user and adds it to Docker groups.
   - Configures Docker Compose.
   - Deploys a Dockerized application.

---

## Key Components

### Terraform Configuration

- Provisions:
  - A VPC, subnets, and an internet gateway.
  - Security groups allowing SSH and application traffic.
  - Three EC2 instances tagged for dynamic inventory.

### Ansible Playbook

- **Playbook: `deploy_use_inventory.yaml`**
  - Installs Docker and Docker Compose.
  - Configures user permissions.
  - Deploys a Dockerized application using `docker-compose`.

---

## Testing and Validation

- Verify Docker installation:
  ```bash
  docker --version
  ```

- Verify Docker Compose installation:
  ```bash
  docker-compose --version
  ```

- Access the application via the public IP of any EC2 instance.

---


## References

- [Terraform Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Ansible AWS EC2 Plugin Documentation](https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html)
- [AWS EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)

---


