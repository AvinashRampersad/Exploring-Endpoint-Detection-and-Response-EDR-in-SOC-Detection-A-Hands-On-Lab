# SOC-Lab

<h2>Description</h2>
The puspose of this lab is to create a controlled environment where I can develop, test, and refine detection strategies for identifying threats and security incidents within an Active Directory environment using Splunk. 
<br />
I'm going to demonstrate how I created a home lab and configured Active directory on a domain controller using vmware. While configuring and running the lab environment I will configure Windows server 2019 to run the following services; DHCP, DNS, Splunk, NAT. 
<br />
I run an attack on a machine using Atomic Red Team and used Splunk to analyze the logs. 

<br />


<h2>Tools Used</h2>

- <b>LimaCharlie EDR</b>
- <b>Sliver</b>
- <b>Vmware</b>

<h2>Environments Used </h2>

- <b>Windows 11</b> 
- <b>Ubantu Linux</b> 


<h2>Walk-through:</h2>

<p align="center">
Network diagram: <br/>
<img src="https://i.imgur.com/28tY5Gq.png" height="60%" width="60%" alt="1"/>
<br />
 <br />
I downloaded and installed VMware with a Windows 10 os for the target system, a Kali Linux os and 2 Windows Server 2019 operating systems for the Active Directory server and Splunk server. Downloaded splunk and installed it into one of the Windows 2019 server vm.<br />
<br />
I assigned the ip addresses of each device to reflect my diagram.   <br/>
<img src="https://i.imgur.com/FTDyEQW.png" height="80%" width="80%" alt="2"/>
<img src="https://i.imgur.com/P5wvhvA.png" height="80%" width="80%" alt="3"/>
<br />
 <br />
I checked connectivity between the servers on the network using ping, it timed out so I turned off the firewall and ping again and it’s connected. <br />
<img src="https://i.imgur.com/mwQw4nM.png" height="80%" width="80%" alt="4"/>
<br />I added a second network adapter to the server that will be the domain controller for internet connectivity. <br />
<img src="https://i.imgur.com/zajx2zd.png" height="80%" width="80%" alt="5"/>
<br />
 <br />
I then added a role to server manager and installed active directory domain services to windows server 2019.  <br />
<img src="https://i.imgur.com/Ko9DKgk.png" height="80%" width="80%" alt="6"/> 
<img src="https://i.imgur.com/kItqBI5.png" height="80%" width="80%" alt="7"/>
 <br />
 <br />
I configured the domain and restarted the server. <br />
<img src="https://i.imgur.com/fSrrxUw.png" height="80%" width="80%" alt="8"/>
<img src="https://i.imgur.com/4Vw4cVS.png" height="80%" width="80%" alt="9"/>
<br />
 <br />
 I created an organizational unit named “admins” for my administrator account. <br />
<img src="https://i.imgur.com/u36Xcun.png" height="80%" width="80%" alt="10"/>
<img src="https://i.imgur.com/pHZJLPz.png" height="80%" width="80%" alt="11"/>
<br />
 <br />
 Then I added a user to create my account. <br />
<img src="https://i.imgur.com/3w6VRbs.png" height="80%" width="80%" alt="12"/>
<img src="https://i.imgur.com/dyCk0mt.png" height="80%" width="80%" alt="13"/>
<br />
 <br />
 After creating the account I added it to the group of Domain Admins. <br />
<img src="https://i.imgur.com/y1Fyylj.png" height="80%" width="80%" alt="14"/>
<br />
 <br />
 Then I add another role to the server for NAT. <br />
<img src="https://i.imgur.com/JGyiKa2.png" height="80%" width="80%" alt="15"/>
<img src="https://i.imgur.com/h9irt70.png" height="80%" width="80%" alt="16"/>
<img src="https://i.imgur.com/srZdzS9.png" height="80%" width="80%" alt="17"/>
<br />
 <br />
 Then I configured the NAT and choose the NIC with DHCP. <br />
<img src="https://i.imgur.com/KEu48DE.png" height="80%" width="80%" alt="18"/>
<img src="https://i.imgur.com/knbnUZS.png" height="80%" width="80%" alt="19"/>
<img src="https://i.imgur.com/cGcdQaL.png" height="80%" width="80%" alt="20"/>
<br />
 <br />
I added a DHCP server to assign IP addresses to other computers in the network. <br />
<img src="https://i.imgur.com/YOt8ikI.png" height="80%" width="80%" alt="21"/>
<br />
 <br />
I configured the DHCP scope to assign IP addresses from a range of 192.168.11.12-100. <br />
<img src="https://i.imgur.com/YHFV7fM.png" height="80%" width="80%" alt="22"/>
<img src="https://i.imgur.com/fR4h7nJ.png" height="80%" width="80%" alt="23"/>
<img src="https://i.imgur.com/P978F8f.png" height="80%" width="80%" alt="24"/>
<img src="https://i.imgur.com/1yqIyPL.png" height="80%" width="80%" alt="25"/>
<img src="https://i.imgur.com/Z03ijJj.png" height="80%" width="80%" alt="26"/>
<img src="https://i.imgur.com/ug8Wmos.png" height="80%" width="80%" alt="27"/>
<img src="https://i.imgur.com/o7CbYum.png" height="80%" width="80%" alt="28"/>
<br />
 <br />
 Then I ran a powershell script to add 1000 users in active directory. <br />
<img src="https://i.imgur.com/xqjlQEV.png" height="80%" width="80%" alt="29"/>
<img src="https://i.imgur.com/4XBrJUs.png" height="80%" width="80%" alt="30"/>
<br />
 <br />
Next I put my windows 10 device on the same network as the server to get internet access through the domain controller’s internal NIC to the external NIC. <br />
<img src="https://i.imgur.com/pPgal9f.png" height="80%" width="80%" alt="31"/>
<img src="https://i.imgur.com/rP8Oo4H.png" height="80%" width="80%" alt="32"/>
<br />
 <br />
 I installed splunk on the other windows server 2019. <br />
<img src="https://i.imgur.com/UfUTE4j.png" height="80%" width="80%" alt="33"/>
<br />
 <br />
I added the splunk server to the homelab domain. <br />
<img src="https://i.imgur.com/f0iNHCk.png" height="80%" width="80%" alt="34"/>
<br />
 <br />
 I then created a splunk admin account and added it to members of domain admins and domain user. <br />
<img src="https://i.imgur.com/54S3jZ7.png" height="80%" width="80%" alt="35"/>
<br />
 <br />
 I added another hard drive for Splunk logs and I put it online, named it SplunkLogs and formatted it for Splunk. <br />
<img src="https://i.imgur.com/7Ti2OPJ.png" height="80%" width="80%" alt="36"/>
<img src="https://i.imgur.com/xGEWoIC.png" height="80%" width="80%" alt="37"/>
<img src="https://i.imgur.com/eQ7M3ep.png" height="80%" width="80%" alt="38"/>
<br />
 <br />
I downloaded Microsoft edge to be able to use splunk because it would not work on internet explorer. <br />
<img src="https://i.imgur.com/2sfeEIr.png" height="80%" width="80%" alt="39"/>
<br />
 <br />
I downloaded Splunk universal forwarder and copied it to the domain controller and windows 10 machine. <br />
 <img src="https://i.imgur.com/O73GBdH.png" height="80%" width="80%" alt="40"/>
 <br />
 <br />
Then I copied the Splunk universal forwarder and splunk add on for windows into the splunk server machine. <br />
<img src="https://i.imgur.com/6OfLPt5.png" height="80%" width="80%" alt="40"/>
<br />
 <br />
  I installed splunk universal forwarder on the domain server and windows 10 device. <br />
<img src="https://i.imgur.com/R2oPtmY.png" height="80%" width="80%" alt="41"/>
<br />
 <br />
 I copied the extracted splunk universal forwarder file into the splunk program files. <br />
<img src="https://i.imgur.com/tbJyhyr.png" height="80%" width="80%" alt="42"/>
<br />
 <br />
I edit the input file to enable system logs. <br />
<img src="https://i.imgur.com/ipTJB7U.png" height="80%" width="80%" alt="43"/>
<br />
 <br />
  I installed splunk add on to my splunk application. <br />
<img src="https://i.imgur.com/RuUO7fm.png" height="80%" width="80%" alt="44"/>
<br />
 <br />
Then I configured the receiving port to add 9997. Now the splunk server will be getting logs forwarded from the devices on the network. <br />
<img src="https://i.imgur.com/2MSQ75o.png" height="80%" width="80%" alt="45"/>
 <br />
 <br />
 On my windows machine I install Atomic Red Team. <br />
<img src="https://i.imgur.com/qoLP2Wk.png" height="80%" width="80%" alt="46"/>
<br />
 <br />
I add an exception to windows defender so it wouldn’t remove any of the Atomic Red Team files when I install it. First I add an exception to the C:\ drive and once it’s installed I add the C:\AtomicRedTeam exception and remove the C:\ exception. <br />
<img src="https://i.imgur.com/BkLZ4N5.png" height="80%" width="80%" alt="47"/>
<img src="https://i.imgur.com/DkFQBw8.png" height="80%" width="80%" alt="48"/>
<br />
 <br />
I opened Splunk and ran a search of my Windows 10 device.<br />
<img src="https://i.imgur.com/2UJl9K2.png" height="80%" width="80%" alt="49"/>
<br />
 <br />
I then invoked an attack from atomic red team command that will create a new user, add it to the admin user group, then delete that user. <br />
<img src="https://i.imgur.com/Krh1COE.png" height="80%" width="80%" alt="50"/>
<br />
 <br />
Then I review the splunk log and ran a search for NewLocalUser to narrow down the results to see this specific event. <br />
<img src="https://i.imgur.com/VWZ7DSn.png" height="80%" width="80%" alt="51"/>
<br />
 <br />
This log shows the new account being created. <br />
<img src="https://i.imgur.com/1xF8I0l.png" height="80%" width="80%" alt="52"/>
<br />
 <br />
This log shows the new account being added to the admin user group.  <br />
<img src="https://i.imgur.com/fnnNZTN.png" height="80%" width="80%" alt="53"/>
<br />
 <br />
This log shows the new account being removed from the admin user group.  <br />
<img src="https://i.imgur.com/e9GgfW7.png" height="80%" width="80%" alt="54"/>
<br />
 <br />
This log shows the account being deleted.  <br />
<img src="https://i.imgur.com/3a5Q8vV.png" height="80%" width="80%" alt="55"/>
<br />
<br />
<br />


</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
