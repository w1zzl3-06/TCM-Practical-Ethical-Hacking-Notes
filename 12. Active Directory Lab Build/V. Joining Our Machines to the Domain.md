# V. Joining Our Machines to the Domain

Boot up the DC and both user machines. And if memory is an issue. Just boot up the DC and one user. Once configuration is done on one user machine, turn it off and move to the next one. The DC machine must stay on at all times.

Open `Network & Settings` > `Change Adapter Options` > `Ethernet` > `Properties` > `Internet Protocol Version 4(TCP/IP v4)` > Select `Use the following DNS server address` and enter your Domain Controller IP into the `Preferred DNS server` field. 

NOTE: In the case where we cannot access our adapter,  type  `Run` in the search bar and typing `ncpa.cpl` to directly access our ethernet.

Click `OK`

Do this for both user machines.

With this our user machines have been directed to our DC.

![49](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/7cd419a8-bf0e-4ffb-a3dc-e3d244351854)
![50](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/aab0d273-694b-44c4-a8ba-16dec246a670)
![51](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/7d6791c7-029d-4f90-b437-5c50fb3e2897)
![52](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/b832b5c1-2651-4a43-aa81-4a6e20716f9e)

Enter our domain name here as well as our DC machine username and password.
![53](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/90fc82e0-6f75-4e9c-a985-6e0c6bcc1dcd)
![54](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/81699f8c-898b-4a8f-bb9c-fad6280f2737)


Click `Next` and restart the machine. Now repeat the same steps for the second user machine.

Login into the DC machine, Wait for Server Manager to start and go to `Tools` > `Active Directory Users and Computers` > `Computers`

Our user machines should appear like below:

![55](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/3384e2f6-2de7-4cd7-b190-362ca4988dbd)

That’s great!!. But there’s still some more stuff to do (Yay!?)

Back on our user machines we should now be able to login with the same credentials as our DC machine.

Let’s do that and wait for windows to do it’s thing.

We still have some things to setup on these machines such as local administrators and a file share on one of them. Let’s get to it.

![56](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/39c81db5-d8a3-4e00-875a-960dfb9eb882)
![57](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/0c518587-d898-4e0a-86a8-08580cd7bdab)

Click on proceed and set a new password. MAKE SURE THIS PASSWORD IS THE SAME FOR BOTH MACHINES.

Next double-click on the Administrator and untick `Account is disabled`. (For best practice, local admin accounts that are part of a Domain are usually disabled. But we have our reasons….).

Now go to Groups and double-click on Adminsitrators:

![58](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/c3316496-d050-4b31-9532-2f4a51ef84a4)

Double-click it, and select `Add`. 

Remember the users we created when setting up our DC? We’re going to add them here:

![60](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/33397b0c-2bf8-4cba-a2dc-882710239076)

Make sure to click on Check Names to verify if the user exits. You can always go back to the DC machine and check. 

Once that is done, OK then Apply. Next turn on Network discovery:

![61](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/ecb03fde-e496-4e77-93fd-ae7d6817a1b5)

Our Domain Controller should appear:

![62](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/4b55f461-9a74-49c6-925c-6913fcf3ed2e)

Now, let’s go to our second user machine and repeat the same process.

Note that when we reach the part to create another local administrator, make sure to add the user from the previous machine as well as a different user. This will be used for an attack later on. So our list of local administrators on the second user machine should look like this:

![63](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/03f4c594-dfb7-4233-ad2b-35e6a3be40c7)

Make sure to turn on Network Discovery. Now let’s log off and sign in as administrator (the initial user on this machine before joining the domain).

![64](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/bb99e0a8-25be-432f-94b2-b9c14458a625)

We have to add `.\` before the username in order to log in.

From here we are going to connect this to our file share that we created when we were setting up our DC. This is for another attack later on.

![65](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/c0e0aeb3-bba6-495e-b287-f9f9feb8f4af)
![66](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/b7e75ab5-6998-43d1-9182-9145931cf123)
![67](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/ebad9e24-0584-4542-9963-f46e97badc3f)

Click `OK` and our file share should appear under `Network Locations`

![68](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/924850b9-4437-4465-9c91-8e43432af81d)

And with that, our Active Directory lab build is finished. 

Time to break it! 😮
