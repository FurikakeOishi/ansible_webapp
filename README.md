# Ansible Roles: AWS Infrastructure Provisioning & Web App Deployment

## Mission:

This Ansible playbook automates AWS infrastructure provisioning and web application deployment with the following tasks:

1. **Create security group:** Establishes a Security Group on an existing VPC which allows:
   - Inbound: SSH & HTTP access from your local machine, and SSH access from the Ansible host.
   - Outbound: Allow HTTPS & HTTP to any source.

2. **Launch linux instance:** Deploys a Linux instance from a chosen AMI, assigning it the new Security Group.

3. **Deploy web app:** Deploys a Linux web application to the instance, configures it to display the local IP address and listen on port 80.

4. **Displays the webpage's address:** http://{webpage-ip}:80


---



## Prerequisites:
- Ansible host (EC2 instance) with the following IAM role policy:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateSecurityGroup",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:RunInstances",
                "ec2:DescribeInstances",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeInstances",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:DescribeInstanceStatus",
                "ec2:DescribeInstanceAttribute",
                "ec2:DescribeTags",
                "ec2:DescribeSubnets",
                "ec2:DescribeKeyPairs",
                "ec2:CreateTags",
                "ec2-instance-connect:SendSSHPublicKey"
            ],
            "Resource": "*"
        }
    ]
}
```
- Ansible & Python installed on the host
- Copy the repository from the local PC to the Ansible host using SCP to the `~/` directory (commonly `/home/ec2-user`)

## In the Ansible's host /ansible_webapp Directory:
- In `inventories/development/hosts` set:
    ```
    ansible_python_interpreter=<your-python-path>
    ```
- Place encrypted webserver keypair in `ansible_webapp/keys`
- In `aws-task-dev.yml` replace `<key_name>` with the name of your key (without .pem)
- In `roles/infra-setup/defaults/main.yml` configure your environment variables
- Run `ansible-vault encrypt keys/<key_pair_name>`
- Create a pass-file outside the ansible_webapp folder, in `~/` (commonly `/home/ec2-user`), place the pass-file along with the vault pass inside it
- Structure in `~/` should look like this:
    ```
    -ansible_webapp/*
    —pass-file
    ```
- Set global variable for the ansible password: 
    ```
    export ANSIBLE_PASS_FILE=~/pass-file
    ```

## Run the app inside the Ansible host using:
- Run:
    ```
    ansible-playbook playbooks/aws-task-dev.yml
    ```

