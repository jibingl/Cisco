# ASA ASDM-IDM Launcher TLSv1 Issue

### Symptoms
Can not load ASDM-IDM app properly. 
### Causes
Compatibility between ASDM app and Java.
### Solutions: 
#### #1 Pick up proper verions of ASDM and Java.   
For example, asdm version 6.47 & Java version 6u43.
   
#### #2 Use newer/latest versions of ASDM and Java, but it needs some troubleshootings and modifications. 
For example, asdm 7.10.1 & Java 8.    
	
Step-1. Install ASDM-IDM Launcher  
- Connect to ASA5505 through web browser (firefox) and download & install ASDM-IDM Launcher.  
- Check ASDM logs to troubleshoot the launch process. To open logs, click the small java icon at the right corner of the ASDM-IDM Launcher, then you can see logging messages in the ASDM-IDM Java Console.  
- After troubleshooting, our problem is the TLS support issue. There is an error message in ASDM Java console logs, like "... TLS10 provided by server is not supported...".  

Step-2. Modify latest version of Java security settings  
- Run notepad as Administrator.
- Open *C:\Program Files\Java\jre-1.8\lib\security\java.security*.
- Delete **TLSv1** from the line starts with `jdk.tls.disabledAlgorithms=`. Then save. (Don't close notepad)
- From notepad, open the file of *C:\Users\%user%\AppData\LocalLow\Sun\Java\Deployment\deployment.properties*. Before `#java Deployment jre's`, insert a line of `deployment.security.TLSv1=true` if it is not there. Then save.

Step-3. Launch ASDM again to test if it works.  
