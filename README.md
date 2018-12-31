# Moodle upgrade

## Backup data

Run the bash script (`details to come later`) on `msc-educ-smc.cmc.ec.gc.ca` to dump all databases.

Make sure that the databases, moodledata folder and moodle application folders are synced on your computer using Acrosync. If anything goes wrong, you can upload these directories to the server and start over again with no loss of data (`Again, more details later`).

## Put Moodle into maintinance mode

Click on the `Site administration` located in the left hand side menu:

**Insert site admin**

On the `Site administration` page, click on the `Server` tab and then click on the `Maintinance mode` link:

**Insert maintinance_mode**

On the `Maintinance mode` page, modiify the message as needed, select `Enabled` from the `Maintinance mode` dropdown menu and then click on the green `Save changes` button:

**Insert maintinance_enabled**

The site is now in maintinance mode. As admin, you may visit the Moodle site at any time by going to [the Moodle login page](http://msc-educ-smc.cmc.ec.gc.ca/moodle/login/index.php).

## Rename old Moodle application directory and copy over latest version of Moodle

Login to the server through SSH or a tool such as Putty using the correct username and password. _For security purposes, these details will not be listed here_.

We will be using Git to download and checkout the latest version of Moodle, which will then be copied over to the directory that the old version of Moodle is in:

```
# Delete the current Moodle temp directory
> sudo rm -rf /opt/moodle

# Use git to install the Mood repository again
> sudo git clone git://git.moodle.org/moodle.git

# Checkout the branch of Moodle you want to upgrade to.
# In this case, we are using Moodle 3.6
> cd /opt/moodle
> sudo git branch -a
> sudo git branch --track MOODLE_36_STABLE origin/MOODLE_36_STABLE
> sudo git checkout MOODLE_36_STABLE

# Rename the current Moodle folder
> sudo mv /var/www/html/moodle /var/www/html/moodle.backup

# Copy the version of Moodle that you just checked out
# and place it in the same directory as the Moodle folder
# that you just renamed
> sudo cp -R /opt/moodle /var/www/html/
