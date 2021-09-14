# NiceDoggy
A watchdog script for NiceHash OS (NHOS)

This is TERRIBLE code! It has a ton of unnecessary variable passing and I'm certain there are tighter and more efficient ways of doing this. So please contribute!

Well, really it's a terrible shell script but you get my point. I'm not a developer, just a hack.

NiceDoggy is a watchdog script for NiceHash OS (NHOS). It's functionality is relatively simple.



***********************************************
INSTALLATION

1. If you do not already have one, create a directory called "scripts.sh" on the "nhos" partition of the USB drive or HD you boot from.
2. Place "nicedoggy-install.sh" and "nicedoggy.code" into the directory.
3. Reboot or run "nicedoggy-install.sh" from the command line with "sudo sh nicedoggy-install.sh".


The file "nicedoggy-install.sh" is the installer.
The file "nicedoggy.code" contains the actual code.

The nicedoggy installer will put a copy of "nicedoggy.code" into /usr/local/bin/ with the name "nicedoggy".
It will then place and entry into the root crontab to run the script every ten minutes. You can adjust this by changing the installer or simply editing the root crontab to you liking.





Here is the code explained
**************************

#/bin/sh
echo "Scanning for inactive cards";
cd /var/log/nhos/nhm/miners/;

# Grab the most recent mining log (Currently specific to NBminer)
x1=`ls -lt | grep -m2 ""`;

# Strip out the garbage to get a clean filename
x2=`echo ${x1##* }`;

# Pull the most recent hardware performance and look for any instances of "0.000"
x5=$(cat $x2 | grep -wns '============================================================================' -B 13 | tail -n 15 | grep -o "0.000" | head -n 1);

# Strip out the period so it doesn't mess with the reading of the variable
x6=$(echo $x5 | tr -d '.');

# If "0.000" exists in the log a card must be at 0 hashrate
if [[ "$x6" = 0000 ]];
   then
   echo "Inactive card detected. Respawning miners"
   
   # Log the incident
   echo "Inactive card detected. Respawning miners" >> /mnt/nhos/scripts.sh/nicedoggy.log;
   cat $x2 | grep -wns '============================================================================' -B 13 | tail -n 15 >> /mnt/nhos/scripts.sh/nicedoggy.log
   
   # Force NHM3 to restart so all miners restart
   sudo killall -HUP nhm3;
else

   # If everything is fine do nothing. NOTE: This does not go into the log so the log doesn't get enormous.
   echo "All cards functioning normally"
fi





