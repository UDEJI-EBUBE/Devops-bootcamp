# Project1: Creating A Static Website Using Nginx

## Creating an ubuntu server
- I selected the Ec2 option in my aws management console and selected launch instance
- I named my instance **Project1**and selected the **ubuntu** ami![1](img/Screenshot%20(3).png)
-I clicked on the **Create new key pair** button and named my key pair **cube** 
- create the key pair ![2](img/Screenshot%20(4).png)
-Enable SSH, HTTP, and HTTPS access, then proceed to click Launch instance.
- click on view all instances to view your instance
click on the created instance and click on the connect buttin
- copy the command under the ssh client
- open a terminal where your .pem file was stored paste the previously copied command and press enter
![3](img/Screenshot%20(5).png)

## creating and assigning an elastic IP
- Go back to your AWS and select EC2 again if you ve closed it already, if not on the menu bar select **Elastic IPs** under network & security
![4](img/Screenshot%20(6).png)
- click on the **Allocate Elastic UP address** button
- keep the setting unchanged and select allocate
- the prompt will appear on top, click on the button 
![1](img/14.png)
- select the instance you want to associate it with
> [!NOTE]
> The IP address for your instance has been updated to the elastic IP associated with it. Therefore, you will need to SSH into your instance again. Return to the connection page of your instance and copy the new command.





