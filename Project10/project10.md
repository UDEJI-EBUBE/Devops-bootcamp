# Prerequisites for Deploying the Prometheus Stack using Docker Compose

- **AWS Account with a Key Pair:** Ensure you have an AWS account and have created a key pair for accessing EC2 instances.
- **AWS CLI Configured:** Install and configure the AWS CLI on your local machine with your AWS account credentials.
- **Terraform Installed:** Make sure Terraform is installed on your local machine for provisioning infrastructure.

# Spin up an Ubuntu 24.04 EC2 instance we shall be using it for this project

Connect your instance via ssh


Clone the project repository to your instance
```
git clone https://github.com/TobiOlajumoke/prometheus-observability-stack
```
The project code is present in the prometheus-observability-stack folder. cd into the folder.
```
cd prometheus-observability-stack
```
Note: Use visual studio code or relevant IDE to understand the code structure better.

Here is the project structure and config files:
```

├── Makefile
├── README.md
├── alertmanager
│   └── alertmanager.yml
├── docker-compose.yml
├── prometheus
│   ├── alertrules.yml
│   ├── prometheus.yml
│   └── targets.json
└── terraform-aws
    ├── README.md
    ├── modules
    │   ├── ec2
    │   │   ├── main.tf
    │   │   ├── outputs.tf
    │   │   ├── user-data.sh
    │   │   └── variables.tf
    │   └── security-group
    │       ├── main.tf
    │       ├── outputs.tf
    │       └── variables.tf
    ├── prometheus-stack
    │   ├── main.tf
    │   ├── outputs.tf
    │   └── variables.tf
    └── vars
        └── ec2.tfvars
```

## To do this project you need to have the following.

1. A Valid AWS account with full permissions to  and manage AWS VPC service.
2. Setup terraform on an ec2 instance, ensure you have a valid IAM  attached to the instance with VPC provisioning permissions.


We are going to spin up an ec2 instanclowing IAM to it:

- AmazonVPCFullAccess
- AmazonEC2FullAccess
    
 ![1](<img/Screenshot (231).png>)

![2](<img/Screenshot (232).png>)
![3](<img/Screenshot (233).png>)
![4](<img/Screenshot (234).png.>)
![5](<img/Screenshot (235).png.>)

## Now  an EC2 instance, Ubuntu 22.04


## let us the earlier to the instance
![8](<img/Screenshot (236).png>)
![9](<img/Screenshot (237).png>)

##  Connect to your instance via ssh 

- ssh into the instance
- install terraform
```
sudo snap install terraform --classic

```
# Provision Server Using Terraform
Modify the values of ec2.tfvars file present in the terraform-aws/vars folder. You need to replace the values highlighted in bold with values relevant to your AWS account & region.


```
cd terraform-aws/vars
```
If you are using us-west-2, you can continue with the same AMI ID else change the AMI ID 
```
vi ec2.tfvars
```
```
# EC2 Instance Variables
region         = "us-west-2"
ami_id         = "ami-0aff18ec83b712f05"
instance_type  = "t2.large"
key_name       = "add keyname"
instance_count = 1
volume-size = 20

# VPC id
vpc_id  = "add a vpc"
subnet_ids     = "add a subnet"

# Ec2 Tags
name        = "prometheus-stack"
owner       = "devops-mastery"
environment = "dev"
cost_center = "devops-project"
application = "monitoring"

# CIDR Ingress Variables
create_ingress_cidr        = true
ingress_cidr_from_port     = [22, 80, 443, 9090, 9100, 9093, 3000]  # List of from ports
ingress_cidr_to_port       = [22, 80, 443, 9090, 9100, 9093, 3000]  # List of to ports
ingress_cidr_protocol      = ["tcp", "tcp", "tcp", "tcp", "tcp", "tcp", "tcp"]        # Protocol for all rules (you can add more if needed)
ingress_cidr_block         = ["0.0.0.0/0", "0.0.0.0/0", "0.0.0.0/0", "0.0.0.0/0", "0.0.0.0/0", "0.0.0.0/0", "0.0.0.0/0"]
ingress_cidr_description   = ["SSH", "HTTP", "HTTPS", "Prometheus", "Node-exporter", "Alert manager", "Grafana"]

# CIDR Egress Variables
create_egress_cidr    = true
egress_cidr_from_port = [0]
egress_cidr_to_port   = [0]
egress_cidr_protocol  = ["-1"]
egress_cidr_block     = ["0.0.0.0/0"]

```

fill it with the correct variable like this:

![11](img/Screenshot%20(242).png)

Now we can provision the AWS EC2 & Security group using Terraform.

```
cd ../prometheus-stack

```


```
terraform fmt
```

```
terraform init
```

```
terraform validate
```
![12](<img/Screenshot (240).png>)

Execute the plan and apply the changes.

```
terraform plan --var-file=../vars/ec2.tfvars
```

```
terraform apply --var-file=../vars/ec2.tfvars
```

Before typing ‘yes‘ make sure the desired resources are being created. After running Terraform Output should look like the following:

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:
![13](img/Screenshot%20(243).png)

Now we can connect to the AWS EC2 machine just created via ssh. 


# CONNECT TO YOUR NEW INSTANCE 

In previous projeect you've seen how to ssh into an instance

![14](<img/Screenshot (245).png>)

- let's run the following command on our instance

We will check the cloud-init logs to see if the user data script has run successfully.

```
tail /var/log/cloud-init-output.log
```

An example output is shown below. It should show Docker and Docker compose versions as highlighted in the image.


![16](<img/Screenshot (246).png>)

Let’s verify the docker and docker-compose versions again.

```
sudo docker version

```

```
sudo docker-compose version
```
![17](<img/Screenshot (248).png>)


Now that we have the instance ready with the required utilities, let’s deploy the Prometheus stack using docker-compose.

# Deploy Prometheus Stack Using Docker Compose
First, clone the project code repository to the server.
```
git clone https://github.com/TobiOlajumoke/prometheus-observability-stack
```
```
cd prometheus-observability-stack
```
Execute the following make command to update server IP in prometheus config file. Because we are running the/Screenshot (267).png the same server to fetch the server metrics. We also update the alert manager endpoint to the servers public IP address.
```
make all
```
You should see an output as shown below.
![18](<img/Screenshot (249).png>)

## Bring up the stack using Docker Compose. It will deploy Prometheus, Alert manager,Node exporter and Grafana

```
sudo docker-compose up -d
```

On a successful execution, you should see the following output saying Running 5/5

![19](<img/Screenshot (250).png>)

Now, with your servers IP address you can access all the apps on different ports.

1. Prometheus: http://your-public-ip-address:9090
2. Alert Manager: http://your-public-ip-address:9093
3. Grafana: http://your-public-ip-address:3000
4. Now the stack deployment is done. The rest of the configuration and testing will be done the using the GUI.

# Validate Prometheus Node Exporter Metrics
If you visit http://your-public-ip-address:9090, you will be able to access the Prometheus dashboard as shown below.

Validate the targets, rules and configurations as shown below. The target would be Node exporter url.

](README.md) !](img/prometheus1.)

![20](img/Screenshot%20(251).png)
![21](img/Screenshot%20(252).png)

![22](img/Screenshot%20(253).png)
![23](img/Screenshot%20(254).png)

![24](img/Screenshot%20(255).png)

![24](img/Screenshot%20(256).png)
### validating prometheus rules and targets
Now lets execute a promQL statement to view node_cpu_seconds_total metrics scrapped from the node exporter.
```
avg by (instance,mode) (irate(node_cpu_seconds_total{mode!='idle'}[1m]))
```

![25](img/Screenshot%20(257).png)
You should be able to data in graph as shown below.
![26](img/Screenshot%20(258).png)

executing a promQL statement to get graph

# Configure Grafana Dashboards
Now lets configure Grafana dashboards for the Node Exporter metrics.

Grafana can be accessed at: http://your-ip-address:3000

Use admin as username and password to login to Grafana. You can update the password in the next window if required.

Now we need to add prometheus URL as the data source from Connections→ Add new connection→ Prometheus → Add new data source.

Here is the demo.
![28](<img/Screenshot (259).png>)

![29](<img/Screenshot (260).png>)

![30](<img/Screenshot (261).png>)
![31](<img/Screenshot (262).png>)
![32](<img/Screenshot (263).png>)
![33](<img/Screenshot (264).png>)


# Configure Node Exporter Dashboard
Grafana has many node exporter pre-built templates that will give us a ready to use dashboard for the key node exporter metrics.

To import a dashboard, go to Dashboards –> Create Dashboard –> Import Dashboard –> Type 10180 and click load –> Select Prometheus Data source –> Import

Here is the demo.
![34](<img/Screenshot (266).png>)
![35](<img/Screenshot (267).png>)
![36](<img/Screenshot (268).png>)

Once the dashbaord template is imported, you should be able to see all the node exporter metrics as shown below.


# Simulate & Test Alert Manager Alerts
You can access the Alertmanager dashbaord on http://your-ip-address:9093


![39](<img/Screenshot (269).png>)
Alert rules are already backed in to the prometheus configuration through alertrules.yaml. If you go the alerts option in the prometheus menu, you will be able to see the configured alerts as shown below.
 http://your-ip-address:9090
![40](<img/Screenshot (270).png>)
![41](<img/Screenshot (271).png>)

As you can see, all the alerts are in inactive stage. To test the alerts, we need to simulate these alerts using few linux utilities.

You can also check the alert rules using the native promtool prometheus CLI. We need to run promtool command from inside the prometheus container as shown below.
run the commands below in the prometheus server

```
sudo docker exec -it prometheus promtool check rules /etc/prometheus/alertrules.yml
```
# Test: High Storage & CPU Alert
```
dd if=/dev/zero of=testfile_16GB bs=1M count=16384; openssl speed -multi $(nproc --all) &
```

Now we can check the Alert manager UI to confirm the fired alerts.
![43](img/Screenshot%20(272).png)
Now let’s rollback the changes and see the fired alerts has been resolved.

```
rm testfile_16GB && kill $(pgrep openssl)
```
![45](img/Screenshot%20(273).png>)
# Cleanup The Setup
To tear xecute the following terraform command from your workstation.
```
cd prometheus-observability-stack/terraform-aws/prometheus-stack
```
```

terraform destroy --var-file=../vars/ec2.tfvars
```
enter 'yes'
![45](img/Screenshot%20(274).png>)
 

## Terminate the  ec2 instances

# THE END