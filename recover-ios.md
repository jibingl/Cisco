# Recovering IOS File/Image for Cisco Switch/Router

### Boot to ROMMON mode
Connect a switch/router at console port through a terminal app, like Tera Term.  
For old model switchs:  
- Press and hold the "mode" button and then power on the switch.
- Keep holding the "mode" button untill seeing the ROMMON's cmd prompt on your screen. It is roughly about 15 seconds.
For routers:
- Power on the device and keep pressing `break` button of keyboard till ROMMON prompt appears.

### Uploading File over Console Port via `xmodem/ymodem`
1️⃣ Initiate/Mount flash drive under ROMMON mode so that you can access it.
  ```
  switch: flash_init
  switch: dir flash:
  ```
2️⃣ Increase the speed rates of serial connection. It needs to be done on both sides, switch and TeraTerm.
  ```
  switch: set BAUD 115200
  ```
  Edit the searial-rate of the current terminal connection to 115200 as well. Otherwise, you can not type commands on terminal screen properly.  
3️⃣ Set the switch to be ready to recive the file transsion that costs time as the speed limitation.
  ```
  switch: copy xmodem: flash:c2960-lanbasek9-150-2.ie10.bin
  Begin the Xmodem or Xmodem-1K transfer now...
  CCC
  ```
4️⃣ From the terminal (TeraTerm) main manu select **File > Transfer > XMODEM > Send**, then chose your .bin file to start transission.

5️⃣ Check results, reverse the searial-rate and re-boot switch.
  ```
  File "xmodem:" successfully copied to "Flash:c2960-lanbasek9-150-2.ie10.bin"
  switch: dir flash:
  switch: set BAUD 9600
  switch: boot flash:c2960-lanbasek9-150-2.ie10.bin
  Loading "flash:c2960-lanbasek9-150-2.ie10.bin"...@@@@@@@@@@@@@@@@@@@@@@@@@
  ```
6️⃣ Enter config mode and set the system boot image.
  ```
  switch>enable
  switch#configure terminal
  switch(config)#boot system flash:c2960-lanbasek9-150-2.ie10.bin
  switch(config)#show boot
  ........
  switch(config)#exit
  switch#write
  ........
  switch#reload
  ........
  ```
### Recovering the System Image via `tftpdnld`
