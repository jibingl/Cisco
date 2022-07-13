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
```
SW1(config)#logging console [level]                 //Logs to the console. Enabled by defualt 

SW1(config)#logging monitor                         //Logs to a non-console terminal (eg,SSH) during the current session. Enabled by default
SW1(config)#terminal monitor                        //Display logging messages on the current non-console session

SW1(config)#logging buffered [size] [level]         //Logs to an internal buffer. Enabled by default

SW1(config)#logging <server-ip>                       //Logs to a external syslog server/host
 OR
SW1(config)#logging host <server-ip>
```
---
### Configure logging messages to an external Syslog server
##### Configue a Unix Syslog daemon:
1) Log in as root.
2) Confirm the Unix Syslog daemon accepts syslog packets from network.
3) Add a line such as the following to the file etc/syslog.conf: `local7.debug /usr/adm/logs/cisco.log`
4) Creat the log file:
  ```
  $touch /usr/adm/log/cisco.log
  $chmod 666 /usr/adm/log/cisco.log
  ```
5) Ensure the syslog daemon reads the new changes:
  ```
	$kill -HUP `cat /etc/syslog.pid`
  ```
##### Configure Cisco devices:
  ```
  SW1(config)#logging <server-ip>
  SW1(config)#logging trap [level0-7]
  SW1(config)#logging facility {facility-type}          //The default-type is local7
  
  SW1(config)#no logging                                //Remove a syslog server
  SW1(config)#no logging trap                           //Disable logging to syslog servers
  ```
### Useful cmd
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
