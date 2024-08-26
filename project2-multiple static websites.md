# Project 2: Multiple static websites on a single server
## Steps
- Refer to the documentation of the first project [**"Project1"**](https://github.com/UDEJI-EBUBE/Devops-bootcamp/blob/main/project1-Setting%20Up%20A%20Static%20Website%20Using%20Nginx.md) for guidance to perform the following
1. creating an instance
2. creating an elastic ip and associatung it to your instance
3. installing Nginx
## Setting up your website
- download your website template with steps from PROJECT1
- To install the unzip tool with the command `sudo apt install unzip`
-Execute the command to download and unzip your website files `sudo curl -o /var/www/html/2098_health.zip https://www.tooplate.com/zip-templates/2134_gotto_job.zip && sudo unzip -d /var/www/html/ /var/www/html/2134_gotto_job.zip && sudo rm -f /var/www/html/2134_gotto_job.zip`.
![1](img/Screenshot%20(12).png)
- Download the 2nd website template by running the following command
```
sudo curl -o /var/www/html/2127_little_fashion.zip https://www.tooplate.com/zip-templates/2127_little_fashion.zip && sudo unzip -d /var/www/html/ /var/www/html/2127_little_fashion.zip && sudo rm -f /var/www/html/2127_little_fashion.zip
```
- To set up your website's configuration, start by creating a new file in the Nginx sites-available directory. Use the following command to open a blank file in a text editor
`
sudo nano /etc/nginx/sites-available/fashion
`
- copy and paste the following code into the open text editor 
```
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- replace the root directive  
to the directory where 
you downloaded website content is stored.
![2](img/Screenshot%20(15).png)
- Configure your second website by creating a new file in the Nginx sites-available directory with the following command:`sudo nano /etc/nginx/sites-available/job`
- Copy and paste the following code into the open text editor.
```
server {
    listen 80;
    server_name placeholder.com www.placeholder.com;

    root /var/www/html/placeholder.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
- Edit the root directive within your server block to point to the directory where your downloaded website content is stored
![3](img/Screenshot%20(18).png)
- create a symbolic link for both websites by runninf the command.`sudo ln -s /etc/nginx/sites-available/job /etc/nginx/sites-enabled/` `sudo ln -s /etc/nginx/sites-available/health /etc/nginx/sites-enabled/`
- Run the `sudo nginx -t` command to check the syntax of the Nginx configuration file.
- Delete the default files in the sites-available and sites-enabled directories by executing the following commands:
```
sudo rm /etc/nginx/sites-available/default
sudo rm /etc/nginx/sites-enabled/default
```
- Restart the Nginx server by executing the following command: `sudo systemctl restart nginx`.
![4](img/Screenshot%20(16).png)

### Create An A Record

> [!NOTE]
> Before you proceed make sure you already have your domain name and you have a created a hosted zone. visit [**"Project1"**](https://github.com/UDEJI-EBUBE/Devops-bootcamp/blob/main/project1-Setting%20Up%20A%20Static%20Website%20Using%20Nginx.md) for guidance
- In route 53, select the domain name and click on **Create record**.

- Paste your **IP address①** and then click on **Create records②**.

- Click on **Create record** again, to create the record for your sub domain.

- Input the **Record name①**, paste your **IP address②** and then click on **Create records③**.

- Repeat the same process while creating your second subdomain record, and confirm that they both exist in the records list.

![14](img/Screenshot%20(17).png)

- Open your terminal and run **`sudo nano /etc/nginx/sites-available/job`** to edit your settings. Enter the name of your domain and then save your settings.

![15](img/Screenshot%20(18).png)

- Run **`sudo nano /etc/nginx/sites-available/fashion`** to edit your settings. Enter the name of your domain and then save your settings.

![16](img/Screenshot%20(19).png)

- Restart your nginx server by running the **`sudo systemctl restart nginx`** command.

- Go to your domain name in a web browser to verify that your website is accessible.

![17](img/Screenshot%20(25).png)

![18](img/Screenshot%20(26).png)

## Install certbot and request for an SSL/TSL certificate
- install certbot with the following commands
```
sudo apt update
sudo apt install python3-certbot-nginx
sudo certbot --nginx
```
- when you execute the `sudo certbot --nginx` command follow the instructions there and select the sites which you created a record for
![8](img/Screenshot%20(24).png)

- Verify the website's SSL using the OpenSSL utility with the command: openssl s_client -connect job.damitech.com.ng:443
- visit your domains to confirm the certificate

![9](img/Screenshot%20(25).png)

![10](img/Screenshot%20(26).png)
