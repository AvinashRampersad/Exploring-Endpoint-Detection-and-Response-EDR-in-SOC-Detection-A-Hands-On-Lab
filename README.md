# Exploring Endpoint Detection and Response (EDR) in SOC Detection: A Hands-On Lab

<h2>Description</h2>
In this article, I'll discuss a recent project I completed using LimaCharlie EDR and the Sliver adversary emulator. I wanted to complete this project because of Eric Capuano's article titled <a href="https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part">"So you want to be a SOC Analyst?"</a> which provides detailed insights. I encourage you to read his article and try a similar project yourself. 

In this project, we utilize a victim machine (the Windows 11 VM) and an attacker machine (the Ubuntu Server VM). The objective is to detect incoming attacks from the Ubuntu machine by deploying LimaCharlie EDR on the Windows machine. The Ubuntu machine will be equipped with the Sliver adversary emulator to function as a C2 server, issuing remote commands to the Windows machine. Our goal is to create a detection rule in LimaCharlie that identifies an attack and then simulate another attack to try to block it. 
<br />

<h2>Tools Used</h2>

- <b>LimaCharlie EDR</b>
- <b>Sliver</b>
- <b>Vmware</b>

<h2>Environments Used </h2>

- <b>Windows 11 VM</b> 
- <b>Ubantu Linux VM</b> 


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

<img src="https://i.imgur.com/blQLmMu.png" height="70%" width="70%" alt="5"/>
<img src="https://i.imgur.com/yQmJO8F.png" height="70%" width="70%" alt="5"/>

I opened LimaCharlie to analyze the telemetry. From the menu I went to “sensors”then selected my Windows machine. Now I can see all the telemetry from this device that is available. On LimaCharlie I went to the “sensors" tab, I clicked into my windows machine, then I look through the telemetry which is separated into different tabs. I went to the “processes” tab and we can see the executable "PRESIDENTIAL_FUNNY.exe" that we executed is running. Then I went to the ”File System” tab where I can further investigate using the available hash of the file. I uploaded the hash of the file to the built in VirusTotal interface to scan the file to see if it’s malware. Because this executable was just made and not previously identified as malware it came back with no results which is a false negative. 

<img src="https://i.imgur.com/eqeCQr2.png" height="70%" width="70%" alt="5"/>
<img src="https://i.imgur.com/VOvX5TZ.png" height="70%" width="70%" alt="5"/>

<h3>Part 3 - Lsass and Writing Detection Rules</h3>

I went back to the Sliver session and entered the command “getprivs” so I could see what privileges I have. The specific privilege I was looking for is "SeDebugPrivilege". Next I dump the lsass.exe process from memory which is a common credential stealing technique. This dumps the remote process from memory, and save it on your Sliver C2 server.

     procdump -n lsass.exe -s lsass.dmp

<img src="https://i.imgur.com/eSwaXRq.png" height="70%" width="70%" alt="5"/>

This generated more telemetry. It’s time to go back to LimaCharlie and see what was detected. I went to the "Timeline" tab and used the "SENSITIVE_PROCESS_ACCESS" filter, this will find information about the lsass dump attack. 

Now that the process is detected I create a rule for this detection signature by clicking on the box on the top right of the event. I entered the following in the rule: 

<img src="https://i.imgur.com/5NikOBi.png" height="70%" width="70%" alt="5"/>

Detect:

     event: SENSITIVE_PROCESS_ACCESS
     op: ends with
     path: event/*/TARGET/FILE_PATH
     value: lsass.exe

Respond: 

     - action: report
     name: LSASS access

Then I hit the “Test Event” button at the bottom to make sure it’s working and saved it as LSASS access. I then ran the same Lsass dump and went to LimaCharlie then to the “Detection” tab. Here we can see that our detection worked and the response was a report was created. 

<img src="https://i.imgur.com/6ZExeYh.png" height="70%" width="70%" alt="5"/>

<h3>Conclusion</h3>

This lab was very practical and not as difficult as it may seem. It gives a realistic scenario of a defender’s and adversary’s interaction. It allowed me to use and explore an Endpoint Detection and Response tool and Sliver adversary emulator. Getting hands on experience with the tools I used in this lab made me more confident using them as I was able to familiarize myself with the steps and processes to perform different tasks. I did this lab without doing some steps that Eric Capuano did, so if you are interested in completing this lab and want step by step instructions, read Eric Capuano's article titled <a href="https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part">"So you want to be a SOC Analyst?"</a> 

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
