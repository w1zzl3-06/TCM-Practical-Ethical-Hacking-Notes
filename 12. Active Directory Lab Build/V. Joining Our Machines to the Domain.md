# v. Joining Our Machines to the Domain

Boot up the DC and both user machines. And if memory is an issue. Just boot up the DC and one user. Once configuration is done on one user machine, turn it off and move to the next one. The DC machine must stay on at all times.

Open `Network & Settings` > `Change Adapter Options` > `Ethernet` > `Properties` > `Internet Protocol Version 4(TCP/IP v4)` > Select `Use the following DNS server address` and enter your Domain Controller IP into the `Preferred DNS server` field. 

NOTE: In the case where we cannot access our adapter,  type  `Run` in the search bar and typing `ncpa.cpl` to directly access our ethernet.

Click `OK`

Do this for both user machines.

With this our user machines have been directed to our DC.

10.0.2.8

![49.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/49.png)

  

![50.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/50.png)

  

![51.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/51.png)

  

![52.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/52.png)

   Enter our domain name here as well as our DC machine username and password.

  

![53.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/53.png)

  

![54.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/54.png)

  Click `Next` and restart the machine. Now repeat the same steps for the second user machine.

Login into the DC machine, Wait for Server Manager to start and go to `Tools` > `Active Directory Users and Computers` > `Computers`

Our user machines should appear like below:

![55.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/55.png)

That’s great!!. But there’s still some more stuff to do (Yay!?)

Back on our user machines we should now be able to login with the same credentials as our DC machine.

Let’s do that and wait for windows to do it’s thing.

We still have some things to setup on these machines such as local administrators and a file share on one of them. Let’s get to it.

![56.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/56.png)

![57.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/57.png)

Click on proceed and set a new password (To make it easy to remember, make it similar to the current user machine password.)

Next double-click on the Administrator and untick `Account is disabled`. (For best practice, local admin accounts that are part of a Domain are usually disabled. But we have our reasons….).

Now go to Groups and double-click on Adminsitrators:

![58.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/58.png)

Double-click it, and select `Add`. 

Remember the users we created when setting up our DC? We’re going to add them here:

![60.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/60.png)

Make sure to click on Check Names to verify if the user exits. You can always go back to the DC machine and check. 

Once that is done, OK then Apply. Next turn on Network discovery:

![61.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/61.png)

Our Domain Controller should appear:

![62.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/62.png)

Now, let’s go to our second user machine and repeat the same process.

Note that when we reach the part to create another local administrator, make sure to add the user from the previous machine as well as a different user. This will be used for an attack later on. So our list of local administrators on the second user machine should look like this:

![63.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/63.png)

Make sure to turn on Network Discovery. Now let’s log off and sign in as administrator (the initial user on this machine before joining the domain).

![64.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/64.png)

We have to add `.\` before the username in order to log in.

From here we are going to connect this to our file share that we created when we were setting up our DC. This is for another attack later on.

![65.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/65.png)

![66.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/66.png)

![67.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/67.png)

Click `OK` and our file share should appear under `Network Locations`

![68.png](v%20Joining%20Our%20Machines%20to%20the%20Domain%20f2f98d82e83a4f6b96ab3988187b5c0c/68.png)

And with that, our Active Directory lab build is finished. 

Time to break it! 😮