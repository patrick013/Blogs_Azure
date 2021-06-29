# Active Directory and DNS in Azure VM 
### _Configuration of the Active Directory Domain Services role and user account. Add computers to the domain_

[Learning more about Microsoft Active Directory Domain Service ](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview#:~:text=Active%20Directory%20stores%20information%20about%20objects%20on%20the,for%20a%20logical%2C%20hierarchical%20organization%20of%20directory%20information)

## Prerequisites
The following Azure resources are needed for this lab:
- A resource group
- A virtual network
- At least two virtual machine in the same virtual network.
![](https://raw.githubusercontent.com/patrick013/Blogs_Azure/main/Screenshot/Screenshot%202021-06-29%20131115.png?raw=true)
## Add the AD DS Role to Server
- Log into one of the Virtual Machine and add the Active Directory Domain Service through Server Manager. 
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20131658.png?raw=true)
- Do not forget to promote the server to a domain controller after AD DS role being added.
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20132033.png?raw=true)
- In the Active Directory Domain Services Configuration Wizad, add a new forest, for example: mydomain.com
- Install. There may be a couple of yellow warnings, but the installation should not be prevented and complete successfully. After installation, the server would be restarted.
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20132547.png?raw=true)

## Configure the Virtual Network DNS
After you create the domain controller, configure the virtual network to use this server for DNS.
- In the Azure portal, select on the virtual network.
- Under Settings, select DNS Server.
- Select Custom, and type the private IP address of the domain controller.
- Select Save.

## Create Users or Groups
User Full Name | SamAccountName | Group
------ | ------|----------
Tom Smith      | tomsmith    | Agents
Jerry Wesson | jerrywesson   | Agents
Patrick Ruan      |    patrickruan   | Managers
- Open Acitve Directory Administrative Center
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20134209.png?raw=true)
- Create two Groups Agents and Managers: mydomain(local) -> Users -> New -> Group
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20170226.png?raw=true)
- Create users and add user into corresponding Security Group
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20134420.png?raw=true)
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20170427.png?raw=true)
- Check the created users and groups
![](https://raw.githubusercontent.com/patrick013/Blogs_Azure/main/Screenshot/Screenshot%202021-06-29%20134752.png?raw=true)

## Add Client VM into the Domain
- Log into the client VM and open Server Manager, click Configure this local server
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20135419.png?raw=true)
- Click WORKGROUP and click Change
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20141649.png?raw=true)
- Select Domain and type the domian mydomain.com we created before. Enter the DC Server's cretentials
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20141834.png?raw=true)
- The VM would successfully joined the domain if you see the message of "Welcome to the mydomain.com domain."
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20142606.png?raw=true)

## Test the User Account
- Open the Client VM and log it in using a domain user created from previous steps, for example: mydomain\tomsmith
- You might see the error like below: "The connection was denied because the user account is not authorized for remote login"
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20143444.png?raw=true)
- Instead of using domian user account, use the local account to connect the client VM
- Go to Computer Management in Server Manager
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20143822.png?raw=true)
- Computer Management (Local) -> System Tools -> Local Users and Groups -> Groups -> Remote Desktop Users
- Add the users or groups and apply
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20144302.png?raw=true)
- Relogin to the VM using the domain user, it should work.
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20144629.png?raw=true)

## Creating and Configuring a File Share
- Create the folder structure for the new share
-- On DC server, open File Exploer and create a C:\Notes folder
-- Create a text file in the folder
- Configure file permissions on the folder structure. Properties -> Sharing -> Share -> Add Users or groups
-- Grant the Agents security groups Read & Execute, List folder Contents and Read permissions on the folder.  
-- Grant the Managers security groups Full Control permissions on the folder.  
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20174222.png?raw=true)
- Use domain user account to log into the client VM and go to "\\dcserver\Notes", you could see the txt file created. For Agents members, read-only. For Managers members, ready and write
![](https://github.com/patrick013/Blogs_Azure/blob/main/Screenshot/Screenshot%202021-06-29%20175435.png?raw=true)
---
2021-06-29