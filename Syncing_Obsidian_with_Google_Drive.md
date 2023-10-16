# Syncing Obsidian with Google Drive

Syncing Obsidian with Google Drive on Arch Linux can be done by utilizing a synchronization tool or script. One commonly used tool is `rclone`. Here’s a step-by-step guide on how to sync your Obsidian vault with Google Drive using `rclone`.

## Install rclone

1. Open a terminal on your Arch Linux system.
2. Execute the following command to install `rclone` from the community repository:
```bash
sudo pacman -S rclone
```

## Making your own client_id

When you use rclone with Google drive in its default configuration you are using rclone's client_id. This is shared between all the `rclone` users. There is a global rate limit on the number of queries per second that each client_id can do set by Google. rclone already has a high quota and I will continue to make sure it is high enough by contacting Google.
It is strongly recommended to use your own client ID as the default `rclone` ID is heavily used. If you have multiple services running, it is recommended to use an API key for each service. The default Google quota is 10 transactions per second so it is recommended to stay under that number as if you use more than that, it will cause rclone to rate limit and make things slower.


1. Log into the [Google API Console](https://console.developers.google.com/) with your Google account. It doesn't matter what Google account you use. (It need not be the same account as the Google Drive you want to access)
2. Select a project or create a new project.
3. Under "ENABLE APIS AND SERVICES" search for "Drive", and enable the "Google Drive API".
4. Click "Credentials" in the left-side panel (not "Create credentials", which opens the wizard).
5. If you already configured an "Oauth Consent Screen", then skip to the next step; if not, click on "CONFIGURE CONSENT SCREEN" button (near the top right corner of the right panel), then select "External" and click on "CREATE"; on the next screen, enter an "Application name" ("rclone" is OK); enter "User Support Email" (your own email is OK); enter "Developer Contact Email" (your own email is OK); then click on "Save" (all other data is optional). You will also have to add some scopes, including `.../auth/docs` and `.../auth/drive` in order to be able to edit, create and delete files with RClone. You may also want to include the `../auth/drive.metadata.readonly` scope. After adding scopes, click "Save and continue" to add test users. Be sure to add your own account to the test users. Once you've added yourself as a test user and saved the changes, click again on "Credentials" on the left panel to go back to the "Credentials" screen.
6. (PS: if you are a GSuite user, you could also select "Internal" instead of "External" above, but this will restrict API use to Google Workspace users in your organisation).
7. Click on the "+ CREATE CREDENTIALS" button at the top of the screen, then select "OAuth client ID".
8. Choose an application type of "Desktop app" and click "Create". (the default name is fine)
9. It will show you a client ID and client secret. Make a note of these.
10. (If you selected "External" at Step 5 continue to Step 9. If you chose "Internal" you don't need to publish and can skip straight to Step 10 but your destination drive must be part of the same Google Workspace.)
11. Go to "Oauth consent screen" and then click "PUBLISH APP" button and confirm. You will also want to add yourself as a test user.
12. Provide the noted client ID and client secret to `rclone`.

Be aware that, due to the "enhanced security" recently introduced by Google, you are theoretically expected to "submit your app for verification" and then wait a few weeks(!) for their response; in practice, you can go right ahead and use the client ID and client secret with rclone, the only issue will be a very scary confirmation screen shown when you connect via your browser for rclone to be able to get its token-id (but as this only happens during the remote configuration, it's not such a big deal). Keeping the application in "Testing" will work as well, but the limitation is that any grants will expire after a week, which can be annoying to refresh constantly. If, for whatever reason, a short grant time is not a problem, then keeping the application in testing mode would also be sufficient.


## Configure rclone
In the terminal, type the following command to start the configuration process:
```bash
rclone config
```
Follow the prompts to create a new remote connection, choose "Google Drive" as the storage provider, and authenticate `rclone` with your Google Drive account.

## Setup Sync Directory
Decide on a local directory where you'll keep your Obsidian vault, e.g., `~/Obsidian`.
Create a folder in your Google Drive where you want to sync your Obsidian vault.
Setup Sync Command
Create a script file (e.g., sync_obsidian.sh) and open it in a text editor.
Add the following command to sync your local directory with your Google Drive folder:
```bash
rclone copy remote_name:path/to/GoogleDriveFolder ~/Obsidian
rclone sync ~/Obsidian remote_name:path/to/GoogleDriveFolder
```
Replace `remote_name` with the name you gave your remote connection during the rclone configuration, and `path/to/GoogleDriveFolder` with the path to the folder you created in Google Drive.

## Automate Sync
You may want to automate the syncing process. One way is to use a cron job to run your script at regular intervals.
Open the crontab file for editing with the command:
```bash
crontab -e
```

Add a line like the following to run your script every 10 minutes:
```bash
*/10 * * * * /path/to/sync_obsidian.sh
```

## Setup Obsidian
Open Obsidian.
Use the "Open" option to open the local directory (`~/Obsidian`) as your vault.
Now, your Obsidian vault in `~/Obsidian` on your Arch Linux system should sync with the folder in your Google Drive every 10 minutes, or as per the schedule you set in the cron job. Ensure that rclone is correctly configured and authenticated to prevent any sync issues.
