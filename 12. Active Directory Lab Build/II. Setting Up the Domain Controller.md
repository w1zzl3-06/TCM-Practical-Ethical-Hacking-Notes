# ii. Setting Up the Domain Controller

## Setting up the ISO file:

Open up VirtualBox:

- Click on “New”.
- For the ISO image select the Windows Server 2019 image file.

![Create Virtual Machine.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/Create_Virtual_Machine.png)

- Select **Skip unattended installation.**
- Allocate some memory , CPU cores and storage space for the machine.
    
    ![Create Virtual Machine 2.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/Create_Virtual_Machine_2.png)
    

![Create Virtual Machine 3.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/Create_Virtual_Machine_3.png)

Our settings should look like this:

![Create Virtual Machine 4.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/Create_Virtual_Machine_4.png)

## Setting up Windows

- Boot up the machine and select your preferred language and click **Next.**
- Click on **Install Now.**
- Select the option highlighted below.
    
    ![1.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/1.png)
    
- Agree to the license terms.
- Choose **Custom.**
- Select the **New** icon, then apply.
    
    ![2.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/2.png)
    
    ![3.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/3.png)
    
    Wait for Windows to finish the setup.
    
- Setup the user password (make it something simple that meets the standard complexity requirements, i.e. one lowercase and uppercase, a symbol, special character and a number).
- At the login page use `CTRL + ALT + DEL` and enter the password.
    - NOTE for Virtual Box you might need to use `RIGHT CTRL + ALT + WIN + DEL` for it to register on the VM.
- For VirtualBox, Install VboxGuestAdditions to fix the screen size issue.
    
    ![4.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/4.png)
    
    ![5.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/5.png)
    
    ![6.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/6.png)
    
- Click on the installer and wait for it to finish, ****then **Reboot Now.**

## Configuring the Domain Controller

- Time to rename the domain controller. Go to the search bar and type “name” then `View your PC name` > `Rename this PC`. The PC name can be anything.
    
    ![7.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/7.png)
    
- Restart the VM for the changes to take effect.
- Now head over to the Server Manager Dashboard (It should always open on startup).
- Let’s make this PC a Domain Controller by going to `**Manage**` > `**Add Roles and Features**` > **Next** > `**Role-based or feature based installation**` > **Next > `Select a server from the server pool`** > **Next > Select `Active Directory Domain Services` > `Add features` > Next > Next > Select `Restart the destination server automatically if required` > Install > After installation is done, select `Promote this server to a domain controller`.**
    
    ![8.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/8.png)
    
    ![9.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/9.png)
    
- We can name this whatever we want but make sure to add a .local at the end for good measure. Then click **Next.**
    
    ![10.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/10.png)
    
- The password can be the same as the Administrator password. Click **Next.**
- On the next screen, leave the option unselected and click on **Next.**
- Wait for the NetBIOS domain to populate.
    
    ![11.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/11.png)
    
- Keep clicking on next till we reach the **Prerequisites Check** page. Wait for it to finish and we should get a confirmation.
    
    ![12.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/12.png)
    
- Click on **Install.**
- A Sign out prompt should appear and the VM should reboot.
- Our login screen should now have the name of the domain controller next to our username:
    
    ![13.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/13.png)
    
- Our default password should work as usual.
- Time to add Certificate services (used to verify identities in a DC) by going to the Server Manager dashboard, `**Manage**` > `**Add Roles and Features**` > **Next** > `**Role-based or feature based installation**` > **Next > `Select a server from the server pool`** > **Next > Select `Active Directory Certificate Services` `Add features` > Next >** On the Features page click **Next** > **Next** again > On the **Select role services** page, make sure `Certification Authority` is selected > **Next > Select `Restart the destination server automatically if required` > Install.**
    
    ![14.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/14.png)
    
    ![15.png](ii%20Setting%20Up%20the%20Domain%20Controller%20b28957af12df4daa9db7fe26e44601e8/15.png)
    
- Click **Next** till we reach the **Validity Period**  page, set it to **99** years.
- Click **Next.** Then **Configure.**
- Close the windows and restart the VM.