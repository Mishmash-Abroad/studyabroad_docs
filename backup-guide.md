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

`python manage.py decrypt_db input_file output_file --aes-key=$AES_CIPHER_KEY`

## Testing a Backup for Validity
