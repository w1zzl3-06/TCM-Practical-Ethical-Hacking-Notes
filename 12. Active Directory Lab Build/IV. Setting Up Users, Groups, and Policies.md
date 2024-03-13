# iv. Setting Up Users, Groups, and Policies

First turn off all machines and boot up the Domain Controller once again.

On logging in. The server manager dashboard should appear. Head to `Tools` > `Active Directory Users & Computers`

On the left pane, we should see our domain with a drop down containing all our organizational units like Computers, Domain Controllers and Users. 

![18.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/18.png)

For Users, we have a couple of groups mixed in with our User account, including the Domain and Enterprise Administrator accounts that we will attack or take advantage of later. So lets separate them by Right-Clicking on the domain and going to `New` > `Organizational Unit`. Let’s name it Groups or whatever we want and click OK. 

We should have a new folder called `Groups` under our `Users` folder.

Next, go to the `Users` folder and drag everything except the Administrator and Guest to the `Groups` folder. A warning window should pop up, tick the box and click okay.

So our `Users` folder should look like this:

![19.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/19.png)

And our `Groups` folder should be populated with the rest of our groups.

Right click on the Administrator account and select `Copy` . A window should pop up:

![20.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/20.png)

![21.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/21.png)

![22.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/22.png)

We can give this whatever name or password we want. And click `Finish`.

Let’s duplicate the Administrator by copying it again. This time we’re creating a Service account that is a Domain Administrator, which runs a fictitious SQL Server as a service.

![23.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/23.png)

![24.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/24.png)

For the password, unlike the last one, we should give it a password that meets the standard complexity requirements, something like `MYpassword123#`. 

Make sure to write down these passwords somewhere safe just in case.

Add two low-level users by right clicking on the whitespace inside the `Users` folder and going to `New` > `User`.

Give the user a name and password. Make sure to tick the “Password never expires” box. 

Once all accounts are setup. In the end, our `Users` group should look something like this:

![25.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/25.png)

Now, time to setup a file share which we will abuse later on by going to the left and side of the dashboard and choosing `File and Storage Services` > `Shares`>  `TASKS` > `New Share`.

![26.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/26.png)

![27.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/27.png)

![28.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/28.png)

![29.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/29.png)

![30.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/30.png)

![31.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/31.png)

![32.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/32.png)

![33.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/33.png)

Once that’s done. Let’s setup the service account fully by going to the search bar, typing in `cmd`, right-clicking on Command Prompt and choosing `Run as administrator` .

Enter the following command .

```powershell
setspn -a <DOMAIN CONTROLLER NAME>/SQL Service.<DOMAIN NAME>.local:60111 <DOMAIN NAME>\SQL Service
```

Hit `ENTER`.

![34.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/34.png)

Now let’s query to make sure we are seeing our updated object by using the following command: 

```powershell
setspn -T <DOMAIN NAME>.local -Q */*
```

Hit `ENTER`.

![35.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/35.png)

So we now have the SQLService running on port `60111`.

FInally, we are going to set up a Group Policy by typing `group` into the search bar and click on `Group Policy Management`.

![37.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/37.png)

Now go from `Forest:<DOMAIN NAME>.local` > `Domains` > `<DOMAIN NAME>.local` > `RIGHT-CLICK` > `Create a GPO in this domain, and Link it here..`

![38.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/38.png)

![39.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/39.png)

![40.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/40.png)

`RIGHT-CLICK` on the newly created GPO and select `Edit`.

A new window should pop-up and from the left pane go to:

`Computer Configuration` > `Policies` > `Administrative Templates` > `Windows Components` > Select `Windows Defender Antivirus`

![41.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/41.png)

Click on `Turn off Windows Defender Antivirus`.

![42.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/42.png)

Select `Enabled`. Click on `Apply` and `OK`.

Close the window and right-click on the Windows Defender GPO and select `Enforced`

![43.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/43.png)

So now, Windows Defender will be disabled when we join the ONEPIECE.local domain with other Users.

Finally we can check our IP address. By accessing our Command Prompt and typing `ipconfig`

![44.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/44.png)

NOTE: We set our Windows Server and Enterprise machines to the same NAT network as our previous vulnerable machines.

We can also set this machine to a static IP address by going to the searching for `Run` in the search bar and typing `ncpa.cpl`

![45.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/45.png)

![46.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/46.png)

![47.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/47.png)

![48.png](iv%20Setting%20Up%20Users,%20Groups,%20and%20Policies%2052d27d1b5a244b78bf59f01796af92bd/48.png)

Make sure the IP, Subnet and Gateway above coincide to the one in the command prompt.

Phew.. that’s it. For now….