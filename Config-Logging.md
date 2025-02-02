# Configure Cisco Devices Logging

### Enable or disable logging
Notes: Enable and disable logging on Cisco devices. By defualt, logging is enabled. Disabling message logging is actually stop a logging process to which log messages are sent. So, all log messages need to be written to the console instead, which will slow dwon devices.
Disable logging:
```
SW1(config)#no logging on                          //Disable logging
SW1(config)#logging on                             //Re-enable logging
```
---
### Log & Display logging messages onto different destination devices/screens
Syntax
```
SW1(config)#logging console [level]                 //Logs to the console. Enabled by defualt 

SW1(config)#logging monitor                         //Logs to a non-console terminal (eg,SSH) during the current session. Enabled by default
SW1(config)#terminal monitor                        //Display logging messages on the current non-console session

SW1(config)#logging buffered [size] [level]         //Logs to an internal buffer. Enabled by default

SW1(config)#logging <server-ip>                      //Logs to a external syslog server/host
 OR
SW1(config)#logging host <server-ip>
```
---
### Logging Cisco devices' logs to a **rsyslog** server
##### Configue **rsyslog** server:
1) Log in rsyslog server (example, Ubuntu) as root.
2) Confirm the **rsyslog** daemon accepts syslog packets from network.  
	Make sure the **rsyslog** is installed and runnig;
	Make sure the server's firewall allows syslog (port 514); If not, add rules to firewall: 
	```
	sudo ufw allow syslog
	```
3) Configue `/etc/rsyslog.conf` file:  
   **#1** - Directly modify the _/etc/rsyslog.conf_ configuration file.  
        Add the below lines to instruct **rsyslog** to accecpt and how to save log messages from cisco devices.
   	```
	$template	CiscoLog, "/var/log/cisco/%HOSTNAME%.log"
	local7.debug	?CiscoLog
	```
	Uncommit the following lines as shown below:
	```
	# Set udp/514 as protocol and port
 	module(load="imudp")
	input(type="imudp" port="514")
	```
   **#2** - Create your own configuration file under _/etc/ryslog.d/_ folder. **rsyslog** will load all configuration files in the folder.  
	Include all below lines in your configuration file (e.g. remotelogs.conf).  
	```
	$template	CiscoLog, "/var/log/cisco/%HOSTNAME%.log"
	local7.debug	?CiscoLog
 	# Set tdp/514 as protocol and port
	module(load="imtdp")
	input(type="imtdp" port="514")
	```
4) Restart **rsyslog** service.  
5) Verify **rsyslog** network sockets, run `netstat` command and use grep to filter rsyslog string.
	```
	netstat -tulpn | grep rsyslog
	```
	
##### Configure Cisco devices:
1) Configure syslog client (Cisco devices):
	```
	SW1(config)#logging <server-ip>
	SW1(config)#logging trap [level0-7]
	SW1(config)#logging facility {facility-type}          //Normally is local7
	```
  	> Note: `local7` can be other facility types, but must be the same as set in **rsyslog** server.

### Useful commands
To stop sending logs to external syslog server, or remove it at all:
  ```
  SW1(config)#no logging                                //Remove a syslog server
  SW1(config)#no logging trap                           //Disable logging to syslog servers
  ```
To prevent from interrupting by logging messages during typing a command, use `logging synchronous` on the appropriate _line_.  
For example, use the cmd on _console line_:
```
R1(config)#line console 0
R1(config-line)#logging synchronous
```
Set _seqence_ and _time stamp_
```
R1(config)#service timestamp log [datetime | uptime]   
R1(config)#service sequence-numbers                     //Enable sequence number being added to logs
```

### References
https://www.appservgrid.com/paw92/index.php/2019/04/06/how-to-create-a-centralized-log-server-with-rsyslog-in-centos-rhel-7/
https://www.sbarjatiya.com/notes_wiki/index.php/Configuring_rsyslog_to_get_syslog_from_network_devices
https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands
