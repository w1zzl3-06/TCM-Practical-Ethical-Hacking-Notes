# IV. Setting Up Users, Groups, and Policies

First turn off all machines and boot up the Domain Controller once again.

On logging in. The server manager dashboard should appear. Head to `Tools` > `Active Directory Users & Computers`

On the left pane, we should see our domain with a drop down containing all our organizational units like Computers, Domain Controllers and Users. 

![18](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/5a98c431-14c8-4f47-b79c-9225641d9198)

For Users, we have a couple of groups mixed in with our User account, including the Domain and Enterprise Administrator accounts that we will attack or take advantage of later. So lets separate them by Right-Clicking on the domain and going to `New` > `Organizational Unit`. Let’s name it Groups or whatever we want and click OK. 

We should have a new folder called `Groups` under our `Users` folder.

Next, go to the `Users` folder and drag everything except the Administrator and Guest to the `Groups` folder. A warning window should pop up, tick the box and click okay.

So our `Users` folder should look like this:

![19](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/c16eb8c5-f3d3-464f-bd96-4ec596072ea8)

And our `Groups` folder should be populated with the rest of our groups.

Right click on the Administrator account and select `Copy` . A window should pop up:

![20](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/58d87c32-ef8a-496a-af2f-b83f34e08c63)
![21](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/aa48fec2-2f2c-42d0-ae5f-cb340ac94497)
![22](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/0a904eec-9d2a-418a-80aa-7a220e1ba06c)

We can give this whatever name or password we want. And click `Finish`.

Let’s duplicate the Administrator by copying it again. This time we’re creating a Service account that is a Domain Administrator, which runs a fictitious SQL Server as a service.

![23](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/303088cf-e62f-4591-8b2b-5897986c1622)
![24](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/02bc29ce-7977-49cd-b441-a9f8707d27a5)

For the password, unlike the last one, we should give it a password that meets the standard complexity requirements, something like `MYpassword123#`. 

Make sure to write down these passwords somewhere safe just in case.

Add two low-level users by right clicking on the whitespace inside the `Users` folder and going to `New` > `User`.

Give the user a name and password. Make sure to tick the “Password never expires” box. 

Once all accounts are setup. In the end, our `Users` group should look something like this:

![25](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/c9a5b87a-e639-4af6-b317-8243cb451af1)

Now, time to setup a file share which we will abuse later on by going to the left and side of the dashboard and choosing `File and Storage Services` > `Shares`>  `TASKS` > `New Share`.

![26](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/1bf364b3-a41a-4a88-af3d-3659c516391d)
![27](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/dc7b5749-a8b0-4143-b1c3-f7a6f075ee33)
![28](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/6b8e8c95-e181-4bb4-ba8c-d616f5f2651c)
![29](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/3ef6d14e-bcde-4ad1-b5aa-2385640bba5c)
![30](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/0cbaaee9-87d1-4a6c-939a-bd42c2438370)
![31](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/c74c2d0a-b7f8-4cd8-a0e4-fbf96f0b7a8c)
![32](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/1af39f38-e80f-4aec-a46f-afbc1e6ba8a9)
![33](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/5e717230-66de-44f2-a5e3-8ac364487052)


Once that’s done. Let’s setup the service account fully by going to the search bar, typing in `cmd`, right-clicking on Command Prompt and choosing `Run as administrator` .

Enter the following command .

```powershell
setspn -a <DOMAIN CONTROLLER NAME>/SQL Service.<DOMAIN NAME>.local:60111 <DOMAIN NAME>\SQL Service
```

Hit `ENTER`.

![34](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/d4985410-78de-4e37-a0ae-0db80b1046af)

Now let’s query to make sure we are seeing our updated object by using the following command: 

```powershell
setspn -T <DOMAIN NAME>.local -Q */*
```

Hit `ENTER`.

![35](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/16f01431-4093-4079-bf26-80a3978aaead)

So we now have the SQLService running on port `60111`.

FInally, we are going to set up a Group Policy by typing `group` into the search bar and click on `Group Policy Management`.

![37](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/51130b80-2801-4bd2-b26a-4e92005aac94)

Now go from `Forest:<DOMAIN NAME>.local` > `Domains` > `<DOMAIN NAME>.local` > `RIGHT-CLICK` > `Create a GPO in this domain, and Link it here..`

![38](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/76e81c31-da67-47fe-8afd-3c98ba9d3500)
![39](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/1ecb759a-1158-48eb-a72e-84f1fc12d055)
![40](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/e603ee33-e732-442d-8d05-58ef0fce76e5)

`RIGHT-CLICK` on the newly created GPO and select `Edit`.

A new window should pop-up and from the left pane go to:

`Computer Configuration` > `Policies` > `Administrative Templates` > `Windows Components` > Select `Windows Defender Antivirus`

![41](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/8bf6696a-986a-44fb-afd8-6c55044b00f8)

Click on `Turn off Windows Defender Antivirus`.

![42](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/6f705323-7308-4592-8642-110828410ba2)

Select `Enabled`. Click on `Apply` and `OK`.

Close the window and right-click on the Windows Defender GPO and select `Enforced`

![43](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/cc3d1792-f8f9-4551-87f4-b3f5846ef285)

So now, Windows Defender will be disabled when we join the ONEPIECE.local domain with other Users.

Finally we can check our IP address. By accessing our Command Prompt and typing `ipconfig`

![44](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/b1310682-71ec-4ca8-921a-20410d91ab7f)

NOTE: We set our Windows Server and Enterprise machines to the same NAT network as our previous vulnerable machines.

We can also set this machine to a static IP address by going to the searching for `Run` in the search bar and typing `ncpa.cpl`

![45](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/bdd69c32-a6fe-4c25-a539-80aef9636f07)
![46](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/36c89cb2-3c5c-447c-838d-a66a4c4fd3a0)
![47](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/06bf4922-3f5a-4f86-9a86-ccc2f17b8e7b)
![48](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/9950c117-bdd0-459c-afef-deed39e4d199)

Make sure the IP, Subnet and Gateway above coincide to the one in the command prompt.

Phew.. that’s it. For now….
