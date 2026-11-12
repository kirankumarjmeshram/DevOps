# Day 2 - Services & Tag

## 1. Ansible - Install & Enable Nginx

```YAML
name: Install and start Nginx on Pathnex
host: all
become: yes

tasks:
	- name: Install nginx
      yum:
      	name: nginx
        state: present

    -name: Enable nginx
     service:
      name: nginx
      state: started
      enable: yes
```

	This task has important properties:

| Property      | Meaning                                                      |
| ------------- | ------------------------------------------------------------ |
| state: enable | Start Nginx immediately if it is not running                 |
| enabled: yes  | Configure Nginx to start automatically when the server boots |

Difference between ``started`` and ``enable``
	Imagine you starte your EC2 instance.
	 * ``started`` ensures the service is running now.
	* ``enable`` ensures the service starts automatically after reboot.

## 2. Terraform - Create an EC2 Instance

Terraform config:

```JSON
provider = "aws" {
  region = "us-east-1"
}

resource "aws_instance" = "PathnexEC2" {
  ami = "ami-0abcd1234abcd1234"
  instance_type = "r5.2xlarge"

  tags = {
    Name = "Pathnex-Server"
  	Environment = "Training"
  	Owner = "PathnexStudent"
  }
}
```

Here instead on mannually creating EC2 instance we define Infrastructure using code.
Terraform reads your configuration and creates the require infrastructure

### Understanding the configuration:

#### Provider:

```JSON
provider "aws" {
  region: "us-east-1"
}
```

This tells terraform to use AWS in thr US East (N. Virginia) region.

#### Resources:

```JSON
resource "aws_instance" "PathnexEC2" { ... }
```

The syntax is

```
resource "RESOURCE_TYPE" "RESOURCE_NAME"
```

#### Instance Type

```JSON
instance_type = "r5.2xlarge"
```

r5.2xlarge have 8vCPUs and 64GiB RAM

#### Terraform Tags:

```JSON
tage = {
  Name = "Pathnex-Server"
  Environment = "Training"
  Owner = "PathnexStudent"
}
```

Tags are key-value pairs used to identify and organize AWS resources

| Tag             | Purpose                        |
| --------------- | ------------------------------ |
| ``Name``        | Identify the EC2 instance      |
| ``Environment`` | Identify who owns the resource |
| ``Owner``       | Identify who owns the resource |

#### How to execute terraform

save the config as main.tf

```bash
terraform init
terraform validate
terraform plan
terraform apply
```

To delete the infra after running

```bash
terraform destroy
```

## 3. Kubernets - Deployment with 2 Replicas

Kubernates YAML:

```YAML
apiVersion: apps/v1
kind: Deployment

metadata:
    name: Pathnex_deployment

spec:
    replicas: 2
    selector:
        matchLabels:
            app: pathnex-app
    template:
        metadata: 
            labels:
                app: pathnex-app

        spec:
            containers:
                - name: app
                  image: nginx

                  port:
                    - cointerPort: 80
```

#### Run Deployment

save config as deployment.yaml:
``kubectl apply -f deployment.yaml``

Check the deployment:
`kubectl get deployments`

Check the Pods:
`kubectl get pods`

**Pod :** Runs your application container(s).
**Deployment :** Manages the desired number of Pods and their updates and replacements

## 4. Shell Script - Disk Usages

```Shell
#!/bin/bash
df -h
```

``df`` : disk file system
``h`` : human readable

#### Run script

create file ``nano disk-usage.sh``
add the script above and save and then exit
make it executable ``chmod +x disk-usage.sh``
execute ``./disk-usages.sh`` or ``bash disk-usage.sh``

## 5. Docker & Container

command 1: ``docker pull ubuntu`` : downloads the ubuntu image from docker hub
command 2: ``docker images`` : display the images locally
command 3: ``dockes ps -a`` display all containers, including stopped ones

## 6. Docker File

dockerfile:

```YAML
FROM ubuntu:22.04
RUN apt update
CMD ["echo", "Hello Pathnex"]
```
