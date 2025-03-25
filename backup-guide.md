# Backups
## How to run the project's backup solution

## Configuring Backups from Scratch

## Step 1: Follow the Deployment Guide
Follow the deployment guide as specified [here](https://duke-ece-458.gitbook.io/mishmash/dev-docs/deployment-guide)

## Step 2: 

## Restoring the Database to any Given Backup

### Step 1: Choose a backup

Log into the backup server using the proper credentials and choose a backup to go back to from all available in the folders.
As discussed there are three folders
- dev_backups
- test_backups
- prod_backups
Each of these have subfolders such as
- daily_backups
- weekly_backups
- monthly_backups

You can choose any file from here. For the purposes of this guide, we will be referring to the backup you would like to recover as recovery.enc and we will be trying to back up the production server.

### Step 2: Copy the file to the production server

Use SCP to copy the file from the backup server to the production server. upload the file to the /studyabroad/backups folder. This will allow us to access it in the studyabroad-backend-1 container (since studyabroad/backups is linked to /app/backups)!

`scp [OPTIONS] [[user@]src_host:]file1 [[user@]dest_host:]file2`

### Step 3: Log into the backend container

Log into the production server and log into the backend container using a command like

`docker exec -u 0 -it studyabroad-backend-1 bash`

### Step 4: Unencrypt the file

Run the python manage command to decrypt the file such as

`python manage.py decrypt_db encrypted_input_file decrypted_output_file.tar.bz2 --aes-key=$AES_CIPHER_KEY`

### Step 5: Unzip the decrypted file

Once you have the decrypted_output_file.tar.bz2, unzip it using a command like this

`bzip2 -d decrypted_output_file.tar.bz2`

This will replace the compressed file with the uncompressed version.

### Step 6: Run python manage.py loaddata

Once you unzip the file, you will see that it reveals a folder with a structure like this

decrypted_uncompressed_output_file
- meta.json
- data.json
- folder_1/data_file_1.png
- folder_1/data_file_2.png
- folder_2/data_file_1.png

This is merely an example. You will want to load the data.json fixture file into the Django database that you are using. This will be done with this command

`python manage.py loaddata data.json`

Next you will want to load the data that was revealed from the folder_1, folder_2, etc into the media folder of the Django database container.

This can be done with a command like

`mv decrypted_uncompressed_output_file/folder_1 /app/media`

### Step 7: Complete!

You are done with loading a backup!

## Testing a Backup for Validity
If you would like to verify that a backup is valid, here is how to do it.

### [Step 1: Choose a backup](#step-1-choose-a-backup)

### Step 2: Deploy a new system

You can follow our deployment guide to see how to deploy a new system

### [Step 3: Load the backup](#restoring-the-database-to-any-given-backup)

### Step 4: Log in to the system with the admin account

Once the system has been loaded with the backed up data, you can log in as the admin account (or any other user) and verify that files and data have been preserved.


