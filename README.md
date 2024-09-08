<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure virtual machines.<br/>


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)



<h2>Deployment and Configuration Steps</h2>

<h3>Step 1: Setup Resources in Azure</h3>

- Create two virtual machines
- The first virtual machine will be the Domain Controller
- Name: DC-1
- Image: Windows Server 2022
		       
<p align="center">
<img src="https://github.com/user-attachments/assets/601a5601-125d-4281-95ba-66362c8d36d0" height="70%" width="70%" alt="Azure Free Account"/>



- Set DC-1's Virtual Network Interface Card (vNIC) private IP address to be static
- Go to DC-1's network settings
- Select Networking
- Select the link next to Network Interface
- Select IP Configurations > ipconfig1
- Change the assignment from dynamic to static 
- This ensures DC-1's IP address will not change
	   
<p align="center">
<img src="https://github.com/user-attachments/assets/47ee25de-aac5-43b8-aebb-9c8934d1deee" height="70%" width="70%" alt="Azure Free Services"/>  
<img src="https://github.com/user-attachments/assets/527f03df-8fa2-4f60-a03e-3a9c94556a18" height="70%" width="70%" alt="Azure Free Services"/> 
<img src="https://github.com/user-attachments/assets/0f31793a-9f2d-4f77-a966-0eee522a2704" height="70%" width="70%" alt="Azure Free Services"/>
</p>


- The second virtual machine will be the Client
- Name: Client-1
- Image: Windows 10 Pro
- Use the same resource group and vNet as DC-1

<p align="center">
<img src="https://github.com/user-attachments/assets/686d1a90-8ccc-40f5-90fc-e93b2d6d783f" height="70%" width="70%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/66a42821-5696-476b-9715-4eef0d077be9" height="70%" width="70%" alt="Azure Free Services"/>
<img src="https://github.com/user-attachments/assets/4cb507fd-5d37-4932-8531-0e1f6c7d2fea" height="70%" width="70%" alt="Azure Free Account"/>
<img src="https://github.com/user-attachments/assets/1cd6742b-bbba-4e7c-ac16-42ed4be1a5da" height="70%" width="70%" alt="Azure Free Account"/>
</p>
  


<h3>Step 2: Ensure Connectivity Between the Client and Domain Controller</h3>

- Login to Client-1 using Microsoft Remote Desktop
- Search for Powershell and open it
- Ping DC-1's private IP Address (for example, 10.0.0.4)
- Type "ping -t 10.0.0.4" into the command-line interface
- The ping request continually  times out due to the firewall settings
- To fix this, we need to enable ICMPv4 on DC-1's local Windows firewall

<p align="center">
<img src="https://github.com/user-attachments/assets/6a21e893-b4cb-409f-93e9-3923298916e6" height="70%" width="70%" alt="Azure Free Account"/>
	
- Login to DC-1 using Microsoft Remote Desktop
- Start > Windows Administrative Tools > Windows Defender Firewall with Advanced Security > Inbound Rules
- Sort the list by protocols
- Find "Core Networking Diagnostics" and "ICMPv4" and enable these two inbound rules

<p align="center">
<img src="https://github.com/user-attachments/assets/0f8483c8-35b7-44a7-a411-89b2337549e4" height="50%" width="50%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/b9326b63-eed2-4506-9fa6-da988b6d9fe8" height="50%" width="50%" alt="Azure Free Account"/> 
</p>

- Log back into Client-1 and the command line will automatically begin pinging DC-1 successfully
    
<p align="center">
<img src="https://github.com/user-attachments/assets/32ab84ec-8984-43cb-9abe-f1b15bcf7ede" height="70%" width="70%" alt="Azure Free Account"/> 


<h3>Step 3: Install Active Directory</h3>

- Log back into DC-1
- Open Server Manager
- Select "Add Roles and Features" > Follow the prompts
- At Server Roles, check "Active Directory Domain Services."
- Select Add Features > select Next
- Complete the installation

<p align="center">
<img src="https://github.com/user-attachments/assets/bcb676a0-267c-46d0-b1b4-17c7a60831fb" height="80%" width="80%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/c12e0f5c-d8f5-4155-b4a9-6c081d03514e" height="50%" width="50%" alt="Azure Free Services"/>
</p>

- At the top right of the Server Manager Dashboard, click on the flag
- Select "Promote This Server to a Domain Controller"

<p align="center">
<img src="https://github.com/user-attachments/assets/7ba16702-48e6-4a0b-b0b6-d7137a768d45" height="70%" width="70%" alt="Azure Free Account"/> 
	
- Select "Add a New Forest"
- Root domain name: mydomain.com
- Select Next
- Create a password
- Select Next and follow the prompts
- Select Install to complete the installation


<p align="center">
<img src="https://github.com/user-attachments/assets/8d03f34c-457f-4fa4-b970-54f0c8281c81" height="70%" width="70%" alt="Azure Free Account"/>
<img src="https://github.com/user-attachments/assets/272e71e0-c9fe-4564-9611-6bda0d8c8cfd" height="70%" width="70%" alt="Azure Free Account"/>
	
- DC-1 will automatically restart
- Log back into DC-1 as user: mydomain.com\labuser               

<p align="center">
<img src="https://github.com/user-attachments/assets/96db5bb6-e8a8-43db-8fa6-505d6efb6907" height="70%" width="70%" alt="Azure Free Account"/> 
	


<h3>Step 4: Create an Admin and Normal User Account in Active Directory v1.15.8</h3>
     
- On DC-1, open Server Manager
- Click Tools at the top-right of the screen
- Select Active Directory Users and Computers

<p align="center">
<img src="https://github.com/user-attachments/assets/6d656d82-d591-466e-8387-d909b3b3dbb1" height="70%" width="70%" alt="Azure Free Account"/> 
	
- Right-click mydomain.com > New > Select Organizational Unit (OU)
- Create two OUs
- Name the first "_EMPLOYEES"
- Name the second "_ADMINS"
	
<p align="center">
<img src="https://github.com/user-attachments/assets/9bdbc227-a6f6-4dc1-95f8-dd848f16bce0" height="70%" width="70%" alt="Azure Free Account"/> 
	
	
- Right-click mydomain.com and click Referesh to sort the new organizational units to the top
- Go to the _ADMINS OU
- Right-click the name of the OU > New > User
- First/Last name: Jane Doe
- User login name: jane_admin
- Select Next 
- Create a password	
- Uncheck all boxes	
- Select Next and then select Finish

<p align="center">
<img src="https://github.com/user-attachments/assets/8ee730ea-e8e7-4b0a-aa36-be16515c2517" height="70%" width="70%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/7448f088-ed1e-4832-a102-e83402a5f90c" height="70%" width="70%" alt="Azure Free Account"/> 
	
- Go to the _ADMINS OU
- Right-click Jane Doe > select Properties
- Click the tab named "Member of" > select Add
- Type in the names of your domain administrators
- Select "Check Names" > OK > Apply
- Log out of DC-1 as "labuser" and log back in as “mydomain.com\jane_admin”



<p align="center">
<img src="https://github.com/user-attachments/assets/5b9581a2-47ad-4928-a6b7-c11ddd220904" height="70%" width="70%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/81f1954e-f1c4-4860-88e7-a7b4ea69ceb1" height="70%" width="70%" alt="Azure Free Services"/>
</p>
 
     

<h3>Step 5: Join Client-1 to your domain (mydomain.com)
</h3>

- Go back to the Azure portal
- Navigate to the Client-1 Virtual Machine
- On the left-hand side of the screen select Networking
- Select the link next to the NIC > select DNS Server > Custom
- Type in DC-1's private IP address
- Click Save
- After it is done updating, select Restart and select Yes

<p align="center">
<img src="https://github.com/user-attachments/assets/59c895b4-2b69-445a-8c3e-103b89d6290d" height="70%" width="70%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/5e697b52-29c7-4030-acc5-67577094fb5c" height="70%" width="70%" alt="Azure Free Services"/>  
<img src="https://github.com/user-attachments/assets/4b1712c7-7e1d-43e0-99dd-f7b2027a6d9d" height="70%" width="70%" alt="Azure Free Services"/>
</p>

- Log back into Client-1 using Microsoft Remote Desktop as the original local admin (labuser)
- Right-click the Start menu and select System
- On right-hand side of the screen, select Rename This PC (Advanced) > Change
- Under "Member of," select Domain
- Type "mydomain.com" and select OK
- Username: mydomain.com\jane_admin
- Type in password and press OK
- Restart the computer

<p align= "center">
<img src="https://github.com/user-attachments/assets/fc468cd5-ed9a-4dea-aa35-4050527c4bb7" height="80%" width="80%" alt="Azure Free Account"/>
<img src="https://github.com/user-attachments/assets/47f3ca3c-4cbc-4ec1-8452-ca311485f09a" height="80%" width="80%" alt="Azure Free Account"/>
</p>

- Log back into DC-1
- Use mydomain.com\labuser
- Click the Start(Windows) button > Windows Administrative Tools >Active Directory Users and Computers
- Right click mydomain.com > New > Select Organizational Unit (OU)
- Create one OU with the name "_CLIENTS"
- Click the folder named "Computers"
- Client-1 should be there
- Drag Client-1 to the _CLIENTS OU folder
- Check to make sure Clients-1 was added to _CLIENTS
- Log out 

<p align="center">
<img src="https://github.com/user-attachments/assets/685f6eab-3061-4ba6-8089-e486d83ddc50" height="80%" width="80%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/2bbebc60-3903-42cd-85be-b3cd1bd0e972" height="50%" width="50%" alt="Azure Free Services"/>
<img src="https://github.com/user-attachments/assets/9270f58b-6db8-4cf2-abdd-f10b7b78e039" height="50%" width="50%" alt="Azure Free Services"/>
<img src="https://github.com/user-attachments/assets/8f2d11ba-dd52-4b68-84ab-8aa60ad071a8" height="50%" width="50%" alt="Azure Free Services"/>

</p>

<h3>Step 6: Setup Remote Desktop for non-administrative users on Client-1
</h3>

- Log back into Client-1
- Use mydomain.com\jane_admin
- Right-click the Start menu and select System
- On the right-hand side of the screen, select Remote Desktop
- Under User Accounts, click "Select Users That Can Remotely Access This PC > select Add
- Type in the name of your domain users
- Select "Check Names" > OK > OK

 
<p align="center">
<img src="https://github.com/user-attachments/assets/4f8477b3-e943-4271-9054-a387fb68033e" height="70%" width="70%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/d0e00e7b-11e1-4617-9133-57d0321f1813" height="60%" width="60%" alt="Azure Free Services"/>
</p>

<h3>Step 7: Create as many additional users as you would like and attempt to log into Client-1 with one of the users' profiles
</h3>

- Log back into DC-1 as jane_admin
- Search for "Powershell_ise,"
- Right-click on Powershell_ise and open it as an administrator
- At the top-left of the screen select New Script and paste the contents of the following script into it
- You can find the script [here](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

<p align="center">
<img src="https://github.com/user-attachments/assets/8bc88319-f7e3-418f-bcc7-6e4a47a38339" height="70%" width="70%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/a1a5f7be-6538-4d48-aa52-3cb2d17059d2" height="70%" width="70%" alt="Azure Free Services"/>
<img src="https://github.com/user-attachments/assets/113cee2e-4ddd-4af0-beba-5a3e834d85af" height="70%" width="70%" alt="Azure Free Services"/>
</p>

- Click the green arrow button near the top-middle of the screen
- This will run the script
- Once the users have been created, go back to Active Directory Users and Computers > mydomain.com > _EMPLOYEES
- You will see all the accounts that were created
- You can now log into Client-1 with one of the accounts that were created
	
<p align="center">
<img src="https://github.com/user-attachments/assets/5fcea2be-8457-4799-97bf-f45a10e600e3" height="80%" width="80%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/d2da4cb3-92f6-4791-8deb-744e8ad6a40f" height="50%" width="50%" alt="Azure Free Services"/>  
<img src="https://github.com/user-attachments/assets/ec9c63cf-7a5f-41dc-8e3b-f44a6f949ea6" height="70%" width="70%" alt="Azure Free Services"/>
</p>

<p align="center">
<img src="https://github.com/user-attachments/assets/5aafda76-9669-4d61-932e-7a76fb6fe4b0" height="70%" width="70%" alt="Azure Free Account"/> 
<img src="https://github.com/user-attachments/assets/4849cf1b-b5b6-49db-ab8b-f15185832b78" height="70%" width="70%" alt="Azure Free Services"/>
</p>
