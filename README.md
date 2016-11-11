# simpleMCD

  This is a simple Minecraft Server Daemon.  It creates a screen session and then pushes commands to it to start and stop the server.
It consists of two files. The first is the simpleMCD.service file.  This file contains the run commands to open the screen session and 
start the minecraft server in this screen session, this file also executes the miencraftStop script to stop the server in the event of a 
server restart, normal shutdown, or if the daemon is stopped.  The second is the minecraftStop script, this issues the stop command to the 
screen session.

By default this daemon is set up for the minecraft files to be located in the /srv/minecraft directory and owned by a user named 
minecraft.
simpleMCD.service should be placed in the /etc/systemd/system directory and owned by root: 

	sudo cp simpleMCD.service /etc/systemd/system

The minecraftStop should be placed with the rest of your minecraft files and ownership set to your minecraft user: 

	cp minecraftStop /srv/minecraft
	sudo chown minecraft:minecraft /srv/minecraft/minecraftStop

The .jar file that starts the server should be renamed from minecraft_server.x.xx.x.jar to minecraft_server.jar (remove version 
numbers,) otherwise every time there is an update to the minecraft server you will need to update the .service file aswell.  
Ensure that the minecraftStop script and the minecraft_server.jar are both executable: 

	chmod ug+x minecraftStop
	chmod ug+x minecraft_server.jar  

If your configuration is the same as the defaults mentioned above then no other configureation should be needed and you can start 
and enable the daemon: 

	sudo systemctl start simpleMCD
	sudo systemctl enable simpleMCD

If this is the first time starting the minecraft server you will need to stop the daemon and sign the EULA file that was created 
in the /srv/minecraft directory during the first startup.  Otherwise your server should be running.  You can enter the console by typing: 

	screen -rS minecraft
	
This opens the screen session the minecraft server is running in.  From here you should see a log of the latest 
activities that have happend on your server.  You can enter console commands here, for a list of avaliable commands:

	/help

The following are advanced configuration options for if you want to change the allotted resources to run the server or if your 
configureation is different from the default settings.  Any configurations will be done in the simpleMCD.service file, after changing this 
file it is required to reload and restart the daemon

	systemctl daemon-reload
	systemctl restart simpleMCD
	
The file is commented thoroghly for configuration purposes but here is a rundown of what can be changed.  
By default this daemon is set to allot 1Gb of ram to running the server but it can be adjusted in the second ExecStart= line.
  
	ExecStart=/usr/bin/screen -S minecraft -X stuff "java -Xmx1G -Xms1G -jar minecraft_server.jar -nogui\r"
	
The first ExecStart= line should be changed if the location of your minecraft files is not the /srv/minecraft directory.

	ExecStart=/usr/bin/screen -S minecraft -X stuff "cd /srv/minecraft\r"

The path to the minecraftStop script should be changed on the ExecStop= line if yours differs.

	ExecStop=/srv/minecraft/minecraftStop

If the user that owns the minecraft files is different, then the correct user should be entered on the User= line.

	User=minecraft
