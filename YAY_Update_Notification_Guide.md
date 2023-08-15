# YAY Update Notification Guide
Our goal is to set up a weekly notification to remind you to run `yay -Syu`. This guide uses `Zenity` for the GUI dialogs and utilizes cron for scheduling.

## 1. Zenity Installation
`Zenity` provides a way to display GUI dialogs from command-line scripts. It's a perfect fit for this task and is available in the Arch repositories.

To install Zenity:

```bash
yay -S zenity
```

## 2. Script Creation
Let's make a script that checks for updates and displays a zenity dialog when updates are available.

### a. Writing the Script
Open a new file:

```bash
vim  ~/scripts/yay-update-notifier.sh
```

Add the following content:

```bash
#!/bin/bash

# Get the X server details
export DISPLAY=:0
export XAUTHORITY=$(ps -C Xorg -f --no-header | sed -n 's/.*-auth //; s/ -[^ ].*// p')

# Check for updates
UPDATES=$(/usr/bin/yay -Qua)

# If there are updates, display the zenity dialog
if [[ ! -z "$UPDATES" ]]; then
    /usr/bin/zenity --text-info --title 'Updates Available' --width 400 --height 300 <<< "$UPDATES"
fi
```
Note: You might need to modify the DISPLAY=:0 value to your default shell's DISPLAY value. Check it using:

```bash
echo $DISPLAY
```

### b. Making the Script Executable
Ensure that the script is executable:

```bash
chmod +x ~/scripts/yay-update-notifier.sh
```

## 3. Setting Up a Cron Job
To schedule the script, we'll use cron. Arch Linux doesn't come with a default cron service, but there are various implementations you can install.

### a. Installing Cron on Arch
If you're interested in using cronie:

```bash
yay -S cronie
sudo systemctl enable cronie.service
sudo systemctl start cronie.service
```
Note: The base system in Arch uses systemd/Timers instead of cron. Thus, you can choose any cron implementation if you don't prefer cronie.

### b. Scheduling the Script
Edit the user's crontab:

```bash
crontab -e
```
To run the script every week, add this line:

```
@weekly /home/<YOUR_USERNAME>/scripts/yay-update-notifier.sh
```
Replace `YOUR_USERNAME` with your actual username.

Save and exit. The script will run once a week and show a zenity dialog if updates are available.

That should be it! You've set up a weekly reminder for running yay -Syu using `Zenity` and `cron` on Arch Linux.

## 4.(Optional) Debugging

### a. Logging Within Your Script:

Modify your script to log its output to a file, this will help in debugging any errors. You can append the following to your cron job:

```
*/1 * * * * /home/<YOUR_USERNAME>/scripts/yay-update-notifier.sh >> /home/<YOUR_USERNAME>/cron.log 2>&1
```
This will redirect both the standard output and standard error of your script to a file named cron.log in your home directory. After waiting a few minutes, you can inspect this log for any error messages.

### b. Cron's Environment:

Create a simple cron job that outputs the environment to a file:

```
*/1 * * * * env > /home/kwei/cron_env.log
```

After a minute, check the cron_env.log file to see the environment in which cron is executing tasks. It's usually much more minimal than a user's environment, and this might give some clues.
