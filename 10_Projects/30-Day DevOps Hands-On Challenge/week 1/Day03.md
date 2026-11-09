# Day 03 — Ansible, Terraform & Kubernetes Basics

## 1. Ansible - Install Apache and Start Service

```YAML
- name: Install and Start Apache
  hosts: all
  become: yes

  task:
  	- name: Install apache
      yum:
      	name:httpd
        state: present

    - name: Start apache
      service:
      	name: httpd
        state: started
        enabled: yes
```

here
``hosts:all`` Execute all server in the inventory
``become: yes`` Execute task with elevated priviledge(sudo)
``yum`` installs packages on RHEL- based Linux Distribution
``name: httpd`` apache packege name on Amazon Linux/RHEL
``state: present`` ensure Apache is installed
``service`` manages linux service
``state: started`` starts apache immediately
``enabled: yes`` starts apache automatically after system reboots


## 2. Terraform - EC2 Instance (t3.medium)



```YAML
provider "aws" {
	region = "us-east-1"
}

resource "aws_instance" "PathnexEC2" {
	ami = "ami-0abcd1234abcd1234"
	instance_type = "t3.medium"

	tags = {
		Name = "Pathnex-T3"
    }
}
```

here
``provider`` specifies AWS as the cloud provider
``region`` specifies the AWS region (US East - N. Virginia)
``resource`` define infrastructure to be created
``aws_instance`` terraform resource type for EC2
``PathnexEC2`` terraform internal resource name
``ami`` amazon machine image used to launch the instance
``instance_type`` define EC2 compute capacity
``t3.medium`` 2 vCPUs and 4GiB RAM
``tag``assign metadata to AWS resources for idntification and management


## 3. Kubernates - Create a ReplicaSet with 3 Replicas


```YAML
apiVersiob: apps/v1
kind: ReplicaSet
metadata: 
	name: pathnex-replicaset
spec:
    replicas: 3
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
                  ports:
                  	-containerPort: 80

                  
            
```



#### 4. Shell Script

```Shell
#! /bin/bash
echo "Current User: $(whoami)"
echo "Current user group: $(groups)"
```



## 5. Docker - Build Nginx web Server



project structure:

Dockerfile and index.html contains in repo

index.html

```HTML
<h1> Hello Pathnex</h1>
```

Dockerfile

```YAML
FROM nginx:latest
Copy index.html /usr/share/nginx/index.html
```
