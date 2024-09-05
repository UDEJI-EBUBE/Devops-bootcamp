# PROJECT 3: SETUP LOAD BALANCING FOR STATIC WEBSITES USING NGINX

## Steps: Using project 1 do the following
- spin up 3 ubuntu servers with distinct names
![1](img/Screenshot%20(29).png)

> [!Note]
> to avoid cost do not associate these instances with any elastic ip address

- install nginx on the 3 servers
- download 2 templates and associate it with two servers using the steps from [**"Project2"**](https://github.com/UDEJI-EBUBE/Devops-bootcamp/blob/main/project2-multiple%20static%20websites.md)

![1](img/Screenshot%20(48).png)

![2](img/Screenshot%20(50).png)

- create a symbolic link for the two websites in their respective terminal using
`sudo ln -s /etc/nginx/sites-available/fitness /etc/nginx/sites-enabled/` for the terminal hosting the gymso template and `sudo ln -s /etc/nginx/sites-available/fashion /etc/nginx/sites-enabled/` for the terminal hosting the fashion templates

![3](img/Screenshot%20(34).png)

![4](img/Screenshot%20(35).png)

- Run the `sudo nginx -t` command to check the syntax of the Nginx configuration file, and when successful run the `sudo systemctl restart nginx` command.
- on both servers run the `sudo rm /etc/nginx/sites-enabled/default`. This will delete the default site-enabled folders and enable Nginx to serve content from your specified website directories. If you don't delete these default folders, you'll continue to see the default Nginx page.
- run the `sudo systemctl restart nginx` again
- check both ip addresses to check if both website sis running

![5](img/Screenshot%20(37).png)

![6](img/Screenshot%20(38).png)

## Configure load balancer

- install nginx on the server you want to use as a load balancer
- Execute `sudo nano /etc/nginx/nginx.conf` to edit your Nginx configuration file.

- Add the following within the http block.

```

    upstream cloudghoul {
    server 1;
    server 2;
    # Add more servers as needed
}

server {
    listen 80;
    server_name <your domain> www.<your domain>;

    location / {
        proxy_pass http://cloudghoul;
    }
}

```
> [!NOTE]
Replace the necessary placeholders as shown in the picture above. Substitute `<server 1>` and `<server 2>` with the actual private IP addresses of your servers. Also, replace `<your domain> www.<your domain>` with your root domain and subdomain name, and update proxy_pass and the other relevant fields accordingly.

![7](img/Screenshot%20(42).png)

- run `sudo nginx -t` to check for syntax error.
- run the command `sudo systemctl restart nginx` to apply changes

## Create a record

- Using the ip address of the load balancer, create a record
- paste your ip address on the first record and click on add another record to create another record with a subdomain of www and the same ip address as the load balncer and click on create record

![8](img/Screenshot%20(43).png)

![9](img/Screenshot%20(44).png)

- Go to your two servers and access your directory to edit your setting by entering the name of your domian and then save the setting.

> [!NOTE]
> make sure to restart your server with the ` sudo systemctl restart nginx` command.
- visit your domain and refresh to make sure the load balancer distributes traffic evenly between your servers

## Certbot for ssl/tls certificate
- follow the process in  [**"Project1"**](https://github.com/UDEJI-EBUBE/Devops-bootcamp/blob/main/project1-Setting%20Up%20A%20Static%20Website%20Using%20Nginx.md) to finish up this step and complete this project.

![9](img/Screenshot%20(52).png)

![9](img/Screenshot%20(53).png)
