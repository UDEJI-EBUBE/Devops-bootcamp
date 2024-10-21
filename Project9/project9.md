# PROJECT 9
## Dockerfile: Building Your First Image

## Step 1 Provision an instance

- Spin up an Ubuntu server

- SSH into the instance 

### Step 2 - Install and start docker

For **Ubuntu**:

1. Update your package index:
   ```bash
   sudo apt update
   ```

2. Install Docker:
   ```bash
   sudo apt install docker.io -y
   ```

3. Start Docker:

   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```
4. Check the Status of Docker
   ```bash
   sudo systemctl status docker
   ```

5. ctrl + c to exit the running docker


### Steps 3. Clone the Docker Project

Once Docker is installed and set up, clone the project repository.

1. Install Git (if not installed):
   ```bash
   
   sudo apt install git -y 
   ```

2. Clone the project repository:
   ```bash
   git clone https://github.com/TobiOlajumoke/docker-flask
   cd docker-flask
   ```

3. The docker file 
```bash
cat dockerfile
```

### Step 4. **Run the Docker Application**

1. **Build the Docker Image**:

   ```bash
   docker build -t flask-application:1.0.0 .
   ```

- Check if the image built

```bash
sudo docker images
```

2.  **Run the Docker Container**:
   ```bash
docker run -d -p 8000:8000 flask-application:1.0.0
   ```

Check if the container is running if it is PROCEED to 3

```bash
sudo docker ps
``` 
# if the container isn't running check the list of all containers

```bash
sudo docker ps -a
```

To troubleshoot or find out why the container "exited" and isn't running you'll check the container logs by running this command 

```bash
sudo docker logs <container_id_or_name>

```

3.  Test in Browser
Now, go to your browser and access your EC2 public IP to check if the app is running properly:

```bash
http://<your-ec2-public-ip>:8000
```
![1](img/Screenshot%20(220).png)

4. The webpage will not work, WHY?

 we have not added the port 8000 to our security group of our instance so do that and try accessing  the EC2 public IP

![2](img/Screenshot%20(226).png)


![3](img/Screenshot%20(221).png)

# Let's push the image to docker hub

---

### Pushing Docker Images to Docker Hub

After successfully building and running your Docker image, you may want to share it with others or deploy it to different environments. Docker Hub is a cloud-based registry service that allows you to store and distribute Docker images.

#### **Step 1: Create a Docker Hub Account**

1. Go to [Docker Hub](https://hub.docker.com/).
2. Sign up for a free account if you don’t have one already.
![4](img/Screenshot%20(229).png)
3. create a repo
![5](img/Screenshot%20(230).png)
#### **Step 2: Log In to Docker Hub from Your Terminal**

Use the Docker CLI to log in to your Docker Hub account:

```bash
sudo docker login
```
![6](img/Screenshot%20(223).png)

You will be prompted to enter your Docker Hub username and password.

#### **Step 3: Tag Your Image**

Before pushing the image, you need to tag it with your Docker Hub username and a repository name. The tagging format is:

```
<your-dockerhub-username>/<repository-name>:<tag>
```

For example:

```bash
sudo docker tag flask-application:1.0.0 yourusername/flask-application:1.0.0
```

#### **Step 4: Push the Image to Docker Hub**

Once your image is tagged, you can push it to Docker Hub using the following command:

```bash
docker push yourusername/flask-application:1.0.0
```
![7](img/Screenshot%20(224).png)

#### **Step 5: Verify the Push**

After the push completes, you can verify that your image is on Docker Hub by visiting your Docker Hub profile and checking the repositories.
![8](img/Screenshot%20(225).png)
---
## WATCH THIS [Video](https://www.youtube.com/watch?v=1ymi24PeF3M) TOO SOLIDIFY YOUR KNOWLEDGE ABOUT DOCKER 

# VISIT THE PROJECT BREAKDOWN FOR DETAILED INFORMATION