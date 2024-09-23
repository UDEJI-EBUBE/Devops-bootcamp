# Project 5: Setup Service Discovery Using Nginx & Consul

## Project 5

|S/N | Project Tasks                                      |
|----|----------------------------------------------------|
| 1  |Deploy 4 Ubuntu Server                              |
| 2  |Allow required ports in the security group          |
| 3  |Set up architecture                                 |
| 4  |Setup Consul Server                                 |
| 5  |Setup Backend Servers                               |
| 6  |Setup Load-Balancer                                 |
| 7  |Validate Service Discovery Setup                    |

## Deploy 4 ubuntu servers
- deploy 4 ubuntu servers with reference from [**"Project1"**](https://github.com/UDEJI-EBUBE/Devops-bootcamp/blob/main/project1-Setting%20Up%20A%20Static%20Website%20Using%20Nginx.md), make sure to name the servers accordingly to properly differenciate them
![1](img/Screenshot%20(118).png)

## Allow Required Ports In The Security Group
The Consul service requires specific ports to function correctly. Please open the following ports in your security group.

|S/N |Port Name  |Protocol      |Default Port   |
|----|-----------|--------------|---------------|
| 1  |DNS        |TCP and UDP   |8600           |
| 2  |HTTP API   |TCP           |8500           |
| 3  |HTTPS API  |TCP           |8501           |
| 4  |gRPC       |TCP           |8502           |
| 5  |gRPC TLS   |TCP           |8503           |
| 6  |Server RPC |TCP           |8300           |
| 7  |LAN Serf   |TCP and UDP   |8301           |
| 8  |WAN Serf   |TCP and UDP   |8302           |

- Select the **checkbox①** next to your instance, click on **Security②**, and then click on the **security group ID③**.

- Click on **Edit inbound rules**.
![3](img/Screenshot%20(117).png)

- Click on **Add rule**.

- Enter the **Port range**.

> [!NOTE]
This port supports both TCP and UDP protocols, so you'll need to configure both.

- Choose the appropriate CIDR block.

- Click on **Add Rule** to specify the port range for the UDP protocol.

- Click on the **Type field①** and choose **Custom UDP**② from the dropdown menu.

- Enter the **Port range** and choose the **CIDR blocks**.

> [!NOTE]
Repeat this process until you've opened up all necessary ports.

- Verify that all the necessary ports are open.
![10](img/Screenshot%20(116).png)

- Click on **Save rules** to apply the updated security group settings.
---

### Setup Consul Server

- SSH into the consul server and run **`sudo apt update`** to refresh the package cache.

- execute the following commands to install Consul.

```
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install consul
```

![11](img/Screenshot%20(120).png)

![12](img/Screenshot%20(121).png)

![13](img/Screenshot%20(122).png)

- Confirm Consul installation by checking its version with the **`consul --version`** command.
![12](img/Screenshot%20(123).png)

- All the Consul server configurations are located in the **`/etc/consul.d`** folder. To configure the Consul server, start by backing up the default configuration file **`consul.hcl`** by renaming it to **`consul.hcl.back`**, using the following command: **`sudo mv /etc/consul.d/consul.hcl /etc/consul.d/consul.hcl.back`**

- Generate an **encrypted key** using the **`consul keygen`** command.
![14](img/Screenshot%20(123).png)

- Create a new file named **`consul.hcl`** in the **`/etc/consul.d`** directory, using the following command: **`sudo vi /etc/consul.d/consul.hcl`**

- Add the following content to the **`consul.hcl`** file, replacing **<YOUR_ENCRYPTED_KEY>** with the encrypted key you generated:

```
"bind_addr" = "0.0.0.0"
"client_addr" = "0.0.0.0"
"data_dir" = "/var/consul"
"encrypt" = "<YOUR_ENCRYPTED_KEY>"
"datacenter" = "dc1"
"ui" = true
"server" = true
"log_level" = "INFO"
```

![15](img/Screenshot%20(124).png)

Save this file after adding the content.

---

- Run the following command to start the Consul server in the background: **`sudo nohup consul agent -dev -config-dir /etc/consul.d/ &`**.

> [!NOTE]
We use the **`-dev`** flag to indicate that we are running a single Consul server in development mode.

- You can check the status of the Consul server with the following command: **`consul members`**.
![17](img/Screenshot%20(125).png)

- If you visit **`<EC2 Consul Server IP>:8500`**, you should be able to access the Consul dashboard.
![18](img/Screenshot%20(130).png)

---

### Setup Backend Servers

**Apply the configurations below on both backend servers:**

- SSH into the backend servers and run **`sudo apt-get update -y`** to update package information.
![19](img/Screenshot%20(131).png)

> [!NOTE]
The `y` flag automatically answers **"yes"** to any prompts during the installation process, ensuring that the installation proceeds without requiring manual confirmation.

- Install Nginx on both instances by running the following command: **`sudo apt install nginx -y`**.

> [!NOTE]
After installing Nginx, navigate to the default HTML directory and modify the index.html file on both servers to differentiate them.

- Navigate to the HTML directory by executing the following command: **`cd /var/www/html`**.

- Open the HTML file with your preferred text editor to make edits: **`sudo vi index.html`**.

- Copy the HTML content below into the index.html file. On the second server, replace **SERVER-01** with **SERVER-02** in the HTML file to differentiate between the two backend servers.

```
<!DOCTYPE html>
<html>
<head>
	<title>Kanekis Backend Server </title>
</head>
<body>
	<h1>This is Backend SERVER-01</h1>
</body>
</html>
```

![21](img/Screenshot%20(132).png)

- Install Consul as an agent on the servers. Run the following commands to install Consul:

```
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install consul
```

![22](img/Screenshot%20(133).png)

- Verify that Consul is installed properly by running the following command: **`consul --version`**.
![23](img/Screenshot%20(134).png)

- Replace the default Consul configuration file **`config.hcl`** located in **`/etc/consul.d`** with your custom **`consul.hcl`** file.

- Rename the default file and create a new one by running the following commands:

```
sudo mv /etc/consul.d/consul.hcl /etc/consul.d/consul.hcl.back
sudo vi /etc/consul.d/consul.hcl
```

- Add the following contents to the file. Replace **`<YOUR_ENCRYPTED_KEY>`①** with your encryption key. Also, replace **`34.201.77.72`②** with your Consul server's IP address.

```
"server" = false
"datacenter" = "dc1"
"data_dir" = "/var/consul"
"encrypt" = "<YOUR_ENCRYPTED_KEY>"
"log_level" = "INFO"
"enable_script_checks" = true
"enable_syslog" = true
"leave_on_terminate" = true
"start_join" = ["34.201.77.72"]
```

![24](img/Screenshot%20(135).png)

- Use the following command to create and edit the file: **`sudo vi /etc/consul.d/backend.hcl`**.

- Add the following contents to the **`backend.hcl`** file and save it.

```
"service" = {
  "Name" = "backend"
  "Port" = 80
  "check" = {
    "args" = ["curl", "localhost"]
    "interval" = "3s"
  }
}
```

![29](img/Screenshot%20(136).png)

This configuration registers your backend servers with the Consul server and sets up a health check that uses curl to test the service every 3 seconds.

- Verify the configurations by executing the following command: **`consul validate /etc/consul.d`**.
![30](img/Screenshot%20(137).png)

- Once all configurations are complete, start the Consul agent with the following command: **`sudo nohup consul agent -config-dir /etc/consul.d/ &`**.
![30](img/Screenshot%20(138).png)

- To verify if everything is working correctly, visit your Consul UI. If you see the backend listed in the UI as depicted below, it indicates that the backend has successfully registered itself with Consul.

![32](img/Screenshot%20(139).png)

![33](img/Screenshot%20(140).png)

![34](img/Screenshot%20(141).png)

![34](img/Screenshot%20(142).png)

---

### Setup Load-Balancer

- Log in to the load-balancer server. Update the package information and install unzip with the following commands:

```
sudo apt-get update -y
sudo apt-get install unzip -y
```

- Install Nginx using the following command: **`sudo apt install nginx -y`**.

- Download the consul-template binary using the following command:

```
sudo curl -L  https://releases.hashicorp.com/consul-template/0.30.0/consul-template_0.30.0_linux_amd64.zip -o /opt/consul-template.zip

sudo unzip /opt/consul-template.zip -d  /usr/local/bin/
```

![36](img/Screenshot%20(144).png)

- To verify the installation of consul-template, check its version with the following command: **`consul-template --version`**.

![37](img/Screenshot%20(145).png)

- Create and edit a file named **`load-balancer.conf.ctmpl`** in the **`/etc/nginx/conf.d`** directory, using the following command: **`sudo vi /etc/nginx/conf.d/load-balancer.conf.ctmpl`**.

- Paste the following content into the file:

```
upstream backend {
 {{- range service "backend" }} 
  server {{ .Address }}:{{ .Port }}; 
 {{- end }} 
}

server {
   listen 80;

   location / {
      proxy_pass http://backend;
   }
}
```

![38](img/Screenshot%20(146).png)

---
.

> [!NOTE]
This setup keeps Nginx's backend server list in sync with Consul's. It ensures that Nginx always routes traffic to the currently available backend servers.

---

- Create a file named **`consul-template.hcl`** in the **`/etc/nginx/conf.d/`** directory. This configuration file is used by **consul-template** to specify details about the Consul server IP and the destination path where the processed load-balancer.conf file will be saved.

Use the following command to create and edit the file: **`sudo vi /etc/nginx/conf.d/consul-template.hcl`**.

- Add the following content to the file, replacing **`<Consul Server IP>`** with your Consul server's IP address. This configuration specifies the Consul server details, the path to the template file, the destination for the rendered Nginx configuration, and the command to reload Nginx after updating the configuration.

```
consul {
 address = "<Consul Server IP>:8500"

 retry {
   enabled  = true
   attempts = 12
   backoff  = "250ms"
 }
}
template {
 source      = "/etc/nginx/conf.d/load-balancer.conf.ctmpl"
 destination = "/etc/nginx/conf.d/load-balancer.conf"
 perms       = 0600
 command = "service nginx reload"
}
```

![39](img/Screenshot%20(149).png)

- Delete the default server configuration to disable it by running the following command: **`sudo rm /etc/nginx/sites-enabled/default`**.

- Restart Nginx to apply the changes by running the following command: **`sudo systemctl restart nginx`**.

- Once configurations are complete, start the Consul Template agent using the following command. It continuously monitors Consul for changes.

```
sudo nohup consul-template -config=/etc/nginx/conf.d/consul-template.hcl &
```

- Upon completion, a load-balancer.conf file will be created with backend server information populated from the Consul service registry.

Now, if you access the load balancer IP in your web browser, it will display the custom HTML content from one of the backend servers. When you refresh the page, the load balancer will route your request to the other backend server, displaying its custom HTML content.

![43](img/Screenshot%20(150).png)

![44](img/Screenshot%20(151).png)

This behavior occurs because the load balancer uses a round-robin algorithm by default, distributing incoming requests evenly across all available backend servers.

---

### Service Discovery Test

Now that everything is set up and running, we will test the function of the consul when we stop one of our server

- Stop one of the backend servers. The Consul server will monitor the health of each registered service. Once a backend server is stopped, Consul will detect the server's unavailability and mark it as unhealthy. The health check for that server will fail, and it will be removed from the load balancer's active pool of servers.

![45](img/Screenshot%20(152).png)

![45](img/Screenshot%20(153).png)

- As a result, the load balancer will only direct traffic to the remaining healthy backend servers. This ensures that your application continues to run smoothly without any disruption to users, demonstrating the effectiveness of your service discovery and health check configuration with Consul and Nginx.

![sd_test](img/Screenshot%20(154).png)

![45](img/Screenshot%20(155).png)

" FOR FURTHER EXPLAINATION VISIT THE BREAKDOWN"
---
---

#### The End Of Project 5
