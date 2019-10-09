LAB 02 - FILE SYSTEM SNAPSHOTS
PEDAGOGICAL OBJECTIVES
Learn how to create snapshots of a file system

Become familiar with rsync synchronization tool

Perform backups to a remote system

TASK 1: LOCAL SYNC
In this task you will create incremental backups of a home directory by using file synchronization and file system snapshots. To store the backup you will use the backup disk you partitioned in the previous lab.

Put the full commands for each step and the output (shortened if too long) into the lab report.

Choose a home directory, preferably your own, for which to create a backup. A full uncompressed backup should fill the ext4 partition of the backup disk to not more than 50%. If the home directory is too big, choose an appropriate sub-tree. To find out how much space is occupied by a directory tree you can use the command du -sh <directory>. The directory will be called the source directory.

On the backup disk, on the ext4 partition, create a directory called <username>_backup that will contain the backup. Change the owner of this directory from root to the owner of the source directory so that (1) you do not need superuser rights to copy files into it and (2) the user can directly read files back from the directory without needing superuser rights. The directory will be called the backup directory.

Perform an initial copy of the source directory to the backup directory.

In the backup directory you will create a series of sub-directories for the backups. Their name consists of a timestamp of the time the backup was made:

* aeinstein_backup
    * 2017-09-25-093533
    * 2017-09-25-101142
    * 2017-09-25-104812
Here the format for the timestamp is <year>-<month>-<day>-<hour><minute><second>. Note that it is good practice to give the times in UTC to be independent of timezones.

To perform the initial copy of the source directory into that directory you can use rsync with the -a and -v options:

    rsync -av <source_directory>/ 2017-09-25-093533
What do these options do?
Specifically, which options are implied by the -a option and what do they do?
How can you use the date command to avoid typing the timestamp of the current time? How do you make date produce UTC time?
How much disk space is used by the backup directory?
Without having modified a file in the source directory do an incremental backup using hard links. As before the name of the destination directory is the current timestamp.

In addition to the rsync options of the previous step, use the following options (use man to find out what they do):

--delete
--link-dest to do an incremental backup using hard links
Note: The --link-dest option takes a directory of a previous backup as parameter. If it is given as a relative path, rsync expects it to be relative not to the current directory, but the destination directory (the last parameter).

How much disk space is used by the backup directory according to the du command? How much by the individual snapshot directories? How do you explain what du displays (if you had to write the du command, how would you count hard links)?

Modify a file in the source directory and perform another incremental backup like in the previous step.

Using the stat command examine the inodes of different versions of a file. Do this for a file that has not changed between backups and for a file that has changed. What do you see?

How much disk space is used by the backup directory?

Delete the initial full backup. What happens to the files in the incremental backup that were hardlinked to the files of the full backup?

TASK 2: SET UP SSH FOR REMOTE LOGIN
In this task you will configure SSH to easily log into a remote virtual machine in a public cloud that will server as a backup destination in the next task.

The remote machine has URL address ait.lan.iict.ch (From Local Network or VPN).

We have created an account for you on the machine. The account name is the part before the @ in your email address. You must replace the . with _.

The account is configured to accept login via SSH using a private key.

In your personal .ssh directory download the key.sec file below this document. Be sure to remove all permissions for group and others from this file.

Test logging into your account on the remote machine using SSH. Log out again.

On your local machine configure an SSH shortcut to the account on the remote machine. Create the file ~/.ssh/config if does not yet exist and add the following lines to it:

# Cloud virtual machine for AIT lab
Host cloudvm
    Hostname ait.lan.iict.ch
    IdentityFile ~/.ssh/key.sec
    User <my_user_name>
Replace the username after User by your account name.

Test this shortcut by typing ssh cloudvm. You should see the command line prompt of the remote machine.

TASK 3: REMOTE SYNC
In this task you will use the remote machine as backup destination.

Put the full commands for each step and the output (shortened if too long) into the lab report.

Create a backup directory on the remote machine as described in Task 1 so that your user can read/write.

Repeat the full backup and the incremental backup of task 1, but with the backup going to the remote machine over SSH. In the rsync command you need to prefix the destination parameter with cloudvm: to tell rsync to use SSH to transfer the data to the remote machine.

Optional: Using a network monitoring tool on your local Linux machine like bmon observe how much network traffic rsync is causing.