<a href="https://github.com/JaeSeoKim/badge42"><img src="https://badge42.vercel.app/api/v2/cl9r3rzu100060fl2eaaan66k/stats?cursusId=21&coalitionId=78" alt="UCINE's 42 stats" /></a>

### This is a very important part of the project. You must pay attention to everything.

# Script

- You have to create a simple script called `monitoring.sh` , it must be developed in bash.

- At server startup, the script will display some information on all terminals every 10 minutes.

- The banner is optional.

- No error must be visible.

Your script must always be able to display the following information:
-------------------------------------------------------------------------------------------
- The architecture of your operating system and its kernel version                         
- The number of physical processors                                                        
- The number of virtual processors                                                         
- The current available RAM on your server and its utilization rate as a percentage        
- The current available memory on your server and its utilization rate as a percentage     
- The current utilization rate of your processors as a percentage                          
- The date and time of the last reboot                                                     
- Whether LVM is active or not                                                             
- The number of active connections                                                         
- The number of users using the server                                                     
- The IPv4 address of your server and its MAC (Media Access Control) address               
- The number of commands executed with the sudo program                                    

 <b>script ?</b> It is a sequence of commands saved in a file that when executed will do the function of each command.

### 5.1 Architecture

In order to see the architecture of the OS and its kernel version we will use the command ```uname-a``` ( "-a" == "--all" ) which will basically print all the information except if the type of processor is unknown or the hardware platform.

### 5.2 Core Physics

In order to display the number of physical cores we will use the /proc/cpuinfo file which provides information about the processor: its type, brand, model, performance, etc. We will use the command ```grep "physical id" /proc/cpuinfo | wc-l``` with the grep command we will search inside the "physical id" file and with wc-l we will count the lines of the grep result. We do this because the way to quantify nuclei is not very common. If there is one processor it will mark 0 and if it has more than one processor it will display all processor information separately by counting the processors using zero notation. In this way we will simply count the lines that there are since it is more comfortable to quantify it that way.

### 5.3 Virtual cores

To be able to show the number of virtual cores is very similar to the previous one. We will use the /proc/cpuinfo file again, but in this case we will use the command ```grep processor /proc/cpuinfo | wc-l```. The use is practically the same as the previous one, only that instead of counting the lines of "physical id" we will do it of processor. We do it this way for the same reason as before, the way to quantize marks 0 if there is a processor.

### 5.4 RAM memory

To show the ram memory we will use the ```free``` command to instantly see information about the ram, the part used, free, reserved for other resources, etc. For more information about the command we will put free--help. -h to show human-readable output.

Once we have executed this command we must filter our search since we do not need all the information it provides us, the first thing we must show is the memory total, for this we will use the ```awk``` command that what this command does It is to process data based on text files, that is, we can use the data that interests us from X file. the same thing with memory used. Finally what we will do is compare if the first word of a row is equal to "Mem:" we will print the third word of that row that will be the memory used. The whole command together would be ```RAM_TOTA=$(free -h | grep Mem | awk '{printf("%dMB", $2)}')```. then ``` RAM_USED=$(free -h | grep Mem | awk '{printf("%d",$3)}')
``` then ```RAM_PERC=$(free -h | grep Mem | awk '{printf("%.2f",$3/$2 * 100)}')``` finally, we will assign the return value of this command to a variable that we will concatenate with other variables so that everything remains the same as specified by the subject.

### 5.5 Disk memory

In order to see the disk memory occupied and available we will use the command ```df --total -h``` which means "disk filesystem" , it is used to obtain a complete summary of the disk space usage. and total to get the total. Then we will do a grep so that it only shows us the lines that contain total, Finally, we will use the awk command to get the value of the third line , As in the sibject indicates the used memory is shown in MB so then we will multiply memory used by 1024. The entire command is as follows: ```DISK_USAGE=$(df --total -h | grep total | awk '{printf("%d/%dGB (%d%%)", $3*1024, $2, $5)}')```.

another thing is that in the subject the total size appears in Gb so we must transform it to Gb , for To do this instead of must dividing the number by 1024 and remove the decimals just add it :) btw $5 is for the percentage.

### 5.6 CPU usage.

In order to see the percentage of CPU usage there's a lot of commands that you can use to do that but i have the best one for you which is```mpstat``` command displays performance statistics for all logical processors in the system. Users can define both, the number of times the statistics are displayed, and the interval at which the data is updated, we will grep all, The entire command is as follows: ```mpstat | grep all | awk '{printf("%.2f%%", 100 - $13)}'```. The result of this command is only a part of the final result since there is still some operation to be done in the script to make it look good. What we would have to do is subtract from 100 the amount that our command has returned to us.

### 5.7 Last reboot

To see the date and time of our last reboot we will use the ```who``` command with the ```-b``` flag since with this flag it will show us on the screen the time of the last system boot. As it has happened to us before, it shows us more information than we want, so we will filter and only show what interests us, for this we will use the awk command and and print the result. The entire command would be as follows: ```who -b | awk '{printf "%s  %s" ,$3,$4}'```. BTW i use %s instead of %d to get the whole string :)

### 5.8 LVM check

To check if LVM is active or not, we will use the lsblk command, it shows us information on all block devices (hard drives, SSDs, memories, etc.) Among all the information it provides, we can see lvm in the type of manager. For this command we will do an if since we will either print Yes or No. Basically the condition we are looking for will be to count the number of lines in which "lvm" appears and if there are more than 0 we will print Yes, if there is 0 it will print No. All the command would be: ```lsblk | grep lvm | wc -l | awk '{if($1) {print "yes"}else {print "no"}}'```.

### 5.9 TCPconnections

To see the number of established TCP connections. We will use the ```ss``` command instead of the deprecated netstat. We will filter with the ```-ta``` flag so that only TCP connections are shown. Finally we will do a grep to see the ones that are established since there is also only listening and we will close with wc-l so that it counts the number of lines. The command looks like this: ```ss-ta | grep ESTAB | wc-l```.

### 5.10 Number of users

We will make use of the ```users``` command that will show us the name of the users that exist, knowing this, we will put wc-w so that it counts the number of words that are in the output of the command. The entire command looks like this ```users | wc-w```.

### 5.11 IP address & MAC

To obtain the host address we will use the ```hostname-I``` command and to obtain the MAC we will use the ```ip link``` command which is used to display or modify network interfaces. As more than one interface appears, IP's etc. We will use the grep command to find what we want and thus be able to print on the screen only what we are asked for. To do this we will put ```ip link | grep "link/ether" | awk '{print $2}'``` and this way we will only print the MAC.
The entire command looks like this 
```IP_ADD=$(hostname -I)``` 
```MAC_ADD=$ip link show | grep link/ether | awk '{print $2}'```.

### 5.12 Number of sudo usage

In order to obtain the number of commands that are executed with sudo, we will just cat the sudo log file using ```cat /var/log/sudo/sudo.log | grep -a COMMAND | wc -l``` and we will put a ```grep COMMAND``` and so only the command lines will appear. and we will finally list the lines by ```wc-l```  To verify that it works correctly we can run the command in the terminal, put a command that includes sudo and run the command again and it should
increase the number of sudo executions.

### 5.13 End result of the script

 Pls Remember not to copy and paste if you don't know how each command works, In the evaluation you must explain each command if the evaluator asks for it.

```
#!/bin/bash

ARCH=$(uname -a)
PCPU=$(grep "physical id" /proc/cpuinfo | wc -l)
VCPU=$(grep  processor /proc/cpuinfo | wc -l)
RAM_TOTA=$(free -h | grep Mem | awk '{printf("%dMB", $2)}')
RAM_USED=$(free -h | grep Mem | awk '{printf("%d",$3)}')
RAM_PERC=$(free -h | grep Mem | awk '{printf("%.2f",$3/$2 * 100)}')
DISK_USAGE=$(df --total -h | grep total | awk '{printf("%d/%dGB (%d%%)", $3*1024, $2, $5)}')
CPU_LOAD=$(mpstat | grep all | awk '{printf("%.2f%%", 100 - $13)}')
LAST_BOOT=$(who -b | awk '{printf "%s  %s" ,$3,$4}')
LVM_USE=$(lsblk | grep lvm | wc -l | awk '{if($1) {print "yes"}else {print "no"}}')
CON_TCP=$(ss -ta | grep ESTAB | wc -l)
USR_LOG=$(users | wc -w)
IP_ADD=$(hostname -I)
MAC_ADD=$(ip link show | grep link/ether | awk '{print $2}')
SUDO=$(cat /var/log/sudo/sudo.log | grep -a COMMAND | wc -l)
wall "
	Architecture	: $ARCH
	Physical CPUs	: $PCPU
	Virtual CPUs	: $VCPU
	Memory Usage	: $RAM_USED/$RAM_TOTA  ($RAM_PERC%)
	Disk Usage      : $DISK_USAGE
	Cpu load	: $CPU_LOAD
	Last boot	: $LAST_BOOT
	Lvm use		: $LVM_USE
	Connections TCP	: $CON_TCP  ESTABLISHED
	User log	: $USR_LOG
	Network		: IP $IP_ADD ($MAC_ADD)
	Sudo		: $SUDO cmd" 
```
## 6-Crontab ⏰

<b>crontab ? </b>It is a background process manager. The indicated processes will be executed at the time you specify in the crontab file.

To have crontab correctly configured we must edit the crontab file with the following command ```sudo crontab-u root-e```.

In the file we must add the following command so that the script is executed every 10 minutes ```*/10 * * * * sh /path of the script```.

<img width="798" alt="Screen Shot 2022-08-03 at 4 40 18" src="https://user-images.githubusercontent.com/66915274/182512395-eaebabc2-5866-4ae3-966c-1a80818cde07.png">

Operation of each crontab parameter:

m ➤ Corresponds to the minute in which the script is going to be executed, the value ranges from 0 to 59.

h ➤ The exact time, the 24-hour format is used, the values ​​range from 0 to 23, with 0 being 12:00 midnight.
sun ➤ refers to the day of the month, for example you can specify 15 if you want to run every 15th.

dow ➤ Means the day of the week, it can be numeric (0 to 7, where 0 and 7 are Sunday) or the first 3 letters of the day in English: mon, tue, wed, thu, fri, sat, sun.

user ➤ Defines the user that is going to execute the command, it can be root, or another different user as long as it has permissions to execute the script.

command ➤ Refers to the command or the absolute path of the script to execute.

#
<br>
<br>
<br>


### ▪️ Differences between apt and aptitude ↙️

Aptitude is an improved version of apt. APT is a lower level package manager and aptitude is a high level package manager. Another big difference is the functionality offered by both tools. Aptitude offers better functionality compared to apt-get. Both are capable of providing the necessary means to perform package management. However, if you are looking for a more feature-rich approach, it should be Aptitude.

### ▪️ What is APPArmor❓

It is a Linux kernel security module that allows the system administrator to restrict the capabilities of a program.

### if you think it has been useful I would really appreciate starred 🌟 
