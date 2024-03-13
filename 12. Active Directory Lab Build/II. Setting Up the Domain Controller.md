# II. Setting Up the Domain Controller

## Setting up the ISO file:

Open up VirtualBox:

- Click on “New”.
- For the ISO image select the Windows Server 2019 image file.

![Create_Virtual_Machine](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/9811f625-29a2-4cb6-9761-ce3ef8604f2c)

- Select **Skip unattended installation.**
- Allocate some memory , CPU cores and storage space for the machine.
    
 ![Create_Virtual_Machine_2](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/20966a43-4b39-4c2e-913a-f5233750b693) 
![Create_Virtual_Machine_3](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/35ee09ff-8e50-4f33-9af0-d7ac16bd3e6d)

Our settings should look like this:

![Create_Virtual_Machine_4](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/c28fbd00-6e79-4a62-a36f-4143ad2638d7)

## Setting up Windows

- Boot up the machine and select your preferred language and click **Next.**
- Click on **Install Now.**
- Select the option highlighted below.
    
![1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/0c30402f-9bd3-45f5-ba1f-fb0489b17834)
    
- Agree to the license terms.
- Choose **Custom.**
- Select the **New** icon, then apply.
    
![2](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/99aa9317-ddf6-42c1-b888-7b1cb99d7087) 
![3](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/e1fc1828-b65b-4538-8fc1-bef60af83c73)

Wait for Windows to finish the setup.
    
- Setup the user password (make it something simple that meets the standard complexity requirements, i.e. one lowercase and uppercase, a symbol, special character and a number).
- At the login page use `CTRL + ALT + DEL` and enter the password.
    - NOTE for Virtual Box you might need to use `RIGHT CTRL + ALT + WIN + DEL` for it to register on the VM.
- For VirtualBox, Install VboxGuestAdditions to fix the screen size issue.
    
![4](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/b10a8313-19c7-4148-980a-786455ef47ec)
![5](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/dc6717fe-b9ca-4a3c-9ea9-650671f40c88)
![6](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/195f8998-c3ac-48b9-b7f7-6ddf623a180b)

- Click on the installer and wait for it to finish, ****then **Reboot Now.**

## Configuring the Domain Controller

- Time to rename the domain controller. Go to the search bar and type “name” then `View your PC name` > `Rename this PC`. The PC name can be anything.
    
![7](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/bec45ff8-7786-46f8-ad28-fc9f5b61530f)
    
- Restart the VM for the changes to take effect.
- Now head over to the Server Manager Dashboard (It should always open on startup).
- Let’s make this PC a Domain Controller by going to `**Manage**` > `**Add Roles and Features**` > **Next** > `**Role-based or feature based installation**` > **Next > `Select a server from the server pool`** > **Next > Select `Active Directory Domain Services` > `Add features` > Next > Next > Select `Restart the destination server automatically if required` > Install > After installation is done, select `Promote this server to a domain controller`.**
    
![8](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/b417d8f3-5701-4893-9da6-18047ebebd1c)
![9](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/80c0eb59-d520-47aa-a898-0b891e347569)

- We can name this whatever we want but make sure to add a .local at the end for good measure. Then click **Next.**

![10](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/2327cdac-dbbf-474a-8a12-b9f0507b8eba)
  
- The password can be the same as the Administrator password. Click **Next.**
- On the next screen, leave the option unselected and click on **Next.**
- Wait for the NetBIOS domain to populate.
    
![11](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/1d8c1000-15b5-4236-9177-641cedd2cd8a)
  
- Keep clicking on next till we reach the **Prerequisites Check** page. Wait for it to finish and we should get a confirmation.
    
![12](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/60a5a889-2457-4cce-8066-eec341390697)

- Click on **Install.**
- A Sign out prompt should appear and the VM should reboot.
- Our login screen should now have the name of the domain controller next to our username:

![13](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/a80eb2d9-9ffe-4ac3-a595-8612218510a2)
   
- Our default password should work as usual.
- Time to add Certificate services (used to verify identities in a DC) by going to the Server Manager dashboard, `**Manage**` > `**Add Roles and Features**` > **Next** > `**Role-based or feature based installation**` > **Next > `Select a server from the server pool`** > **Next > Select `Active Directory Certificate Services` `Add features` > Next >** On the Features page click **Next** > **Next** again > On the **Select role services** page, make sure `Certification Authority` is selected > **Next > Select `Restart the destination server automatically if required` > Install.**
    
![14](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/ad47787d-68d5-4510-aaeb-5dedab9ae87a)
![15](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/bb4e54ff-4c97-4d4f-8961-0dcd70b918f4)
    
- Click **Next** till we reach the **Validity Period**  page, set it to **99** years.
- Click **Next.** Then **Configure.**
- Close the windows and restart the VM.
