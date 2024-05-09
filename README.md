# SOC-Lab

<h2>Description</h2>
In this article, I'll discuss a recent project I completed using LimaCharlie EDR and the Sliver adversary emulator. I wanted to complete this project because of Eric Capuano's article titled "So you want to be a SOC Analyst?" (https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part), which provides detailed insights. I encourage you to read his article and try a similar project yourself. 

In this project, we utilize a victim machine (the Windows 11 VM) and an attacker machine (the Ubuntu Server VM). The objective is to detect incoming attacks from the Ubuntu machine by deploying LimaCharlie EDR on the Windows machine. The Ubuntu machine will be equipped with the Sliver adversary emulator to function as a C2 server, issuing remote commands to the Windows machine. Our goal is to create a detection rule in LimaCharlie that identifies an attack and then simulate another attack to try to block it. 
<br />

<h2>Tools Used</h2>

- <b>LimaCharlie EDR</b>
- <b>Sliver</b>
- <b>Vmware</b>

<h2>Environments Used </h2>

- <b>Windows 11</b> 
- <b>Ubantu Linux</b> 


<h2>Walk-through:</h2>
I download and install VMware Workstation. Then I download Windows 11 dev iso directly from Microsoft and a Ubantu iso and booted up a virtual machine with each of them. For the Ubantu VM I set a static IP address so that it doesn’t change throughout the lab. After setting up the machines I checked connectivity by pinging google.com. 
<img src="https://i.imgur.com/9WTIUdc.png" height="70%" width="70%" alt="5"/>
I needed to tuen off Windows Defender so that I can run the executable that I will create without difficulty. For the Windows 11 VM I permanently disabled Windows Defender which will turn itself back on if I didn't do all the following. In order to do this I had to turn off tamper protection, then disable Windows Defender and then boot into safe mode and disable it again, disable Defender via Group Policy Editor, Disable Defender via Registry, now it is off permanently.
<img src="https://i.imgur.com/Ud4iO8p.png" height="60%" width="60%" alt="5"/>
<img src="https://i.imgur.com/cwrgz9w.png" height="60%" width="60%" alt="5"/>
<br/>

<h3>Part 1 - Installing the Defense and Attack software</h3>

First I signed up for a free account on the LimaCharlie website. After registering, I created an organization name which can be anything. Once the organization was set up, I added the Windows machine as a sensor to send telemetry to LimaCharlie. I opened an administrative PowerShell prompt and installed LimaCharlie using the following command:

     Invoke-WebRequest -Uri https://downloads.limacharlie.io/sensor/windows/64 -Outfile C:\Users\User\Downloads\lc_sensor.exe  

LimaCharlie provided a command to copy and paste into the Windows machine for synchronization. 
I ran the command that I got from LimaCharlie on an administrative command prompt. This allows LimaCharlie to sync their service with my machine. I  went back to LimaCharlie web interface sensors tab to confirm that the sensor synced. 
<img src="https://i.imgur.com/gJzYj4e.png" height="70%" width="70%" alt="5"/>
<img src="https://i.imgur.com/fgPsAVR.png" height="70%" width="70%" alt="5"/>

Open Ubantu machine and install Sliver-Server on it. This will  let us create payloads and be our C2 server 
To download and install Sliver I ran the following command on a root shell:

     wget https://github.com/BishopFox/sliver/releases/download/v1.5.34/sliver-server_linux -O /usr/local/bin/sliver-server
     chmod +x /usr/local/bin/sliver-server
     apt install -y mingw-w64
<img src="https://i.imgur.com/KyzUDTD.png" height="70%" width="70%" alt="5"/>

<h3>Part 2 - Payload Generation and Detection</h3>

After installing Sliver, I then generate the required payload to be delivered to the Windows machine. I logged in as a root user and then change directory to where Sliver was downloaded. I used the “sliver-server” command to launched Sliver. On Sliver I entered “implants” to see payload generated. On a regular Ubantu change directory to the sliver directory and start up a simple python web server using the command: 

     python3 -m http.server 80        
   
I opened to the Windows machine and opened an administrative PowerShell so that I’m able to get the executable I created using Sliver from the python web server I just created. On the Ubantu machine I started Sliver then ran the command “http” to start the http listener to see http connection between the devices once established. On the Windows VM, I opened the executable "PRESIDENTIAL_FUNNY.exe". Now we can see the connection. Run command “sessions” which will show information about the sessions available. Then I used the command “use a3ce8c40" which is the session id that we got from the previous command to activate the session which will start a C2 session. To confirm the connection I ran “netstat” command from the Sliver session and looked for the payload which is highlighted in green because it’s associated with Sliver.

I went to LimaCharlie to analyze the telemetry. From the menu I went to “sensors”then selected my Windows machine. Now I can see all the telemetry from this device that is available. 

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
