# Moodle upgrade

## Backup data

Run the bash script (`details to come later`) on `msc-educ-smc.cmc.ec.gc.ca` to dump all databases.

Make sure that the databases, moodledata folder and moodle application folders are synced on your computer using Acrosync. If anything goes wrong, you can upload these directories to the server and start over again with no loss of data (`Again, more details later`).

## Put Moodle into maintinance mode

Click on the `Site administration` located in the left hand side menu:

![](https://github.com/bovisp/moodle-upgrade/blob/master/site_admin.png)

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
```

## Copy over `config.php` and current theme

Copy Moodle's `config.php` file from the `moodle.backup` folder to the `moodle` folder:

```
> cd /var/www/html
> sudo cp moodle.backup/config.php moodle
```

Copy over the `TCDD Learn` theme from the `moodle.backup` folder to the `moodle` folder:

```
> sudo cp -pr moodle.backup/theme/tcdd_learn moodle/theme/tcdd_learn
```

Go back you your web browser and enter the URL of the Training Portal homepage `http://msc-educ-smc.cmc.ec.gc.ca/moodle/` to finish the upgrade process.

## Upgrade Moodle

When you go to the Training Portal now, this is the page that you should see. Note that the version number that you are upgrading to will be different than the one displayed below:

**Insert upgrade_page_one**

Click on the green `Continue` button.

The next page performs a server check to see if your server meets all of the minimum requirements for a server upgrade. As long as you see the message that indicates that the server meets the minum requirements, click on the green `Continue` button to proceed to the next step:

**Insert upgrade_page_two**

## Upgrading your plugins

Now comes the very very fun part. Copying over your plugins. On the page after the server check page, you will land on a ridiculously long page titled `Plugins check`. The purpose of this page is to show you which plugins (either Moodle core or ones that you created and/or installed) are to be upgraded and which ones are to be deleted. It will also indicate any new plugins (as part of Moodle new core features) that will be installed.

Here's the rub. Many plugins that you installed are in the old `moodle.backup` folder and must first be copied over before you can move away from this page and complete the installation. For Training Portal, there are a lot of them and it is best to pick them off one by one.

Scroll down this page until you see the first plugin that is indicated as `Missing from disk`. In this case it is the `hoppot` plugin:

**Insert hotpt_plugin_error**

If you scroll up the page a bit, you will see that this plugin falls under the `Activity modules` group of plugins:

**Insert activity_modules_heading**

Look down the list of `Activity modules` plugins. Under the name of each plugin is the location of that plugin in the moodle application. In this case you will see that every `Activity module` plugin is located in the `/mod` directory:

**Insert mod_dir**

Scroll back down the list until you see the `hotpot` module that is missing. To copy this plugin over, you need to note down the main folder where it needs to be copied into. In this case it is `/mod`. The name of the specific plugin folder is whatever you see here:

**Insert proper_plugin_folder_name**

In this case it is `hotpot`. So, here is the full command you need to type. **Make sure that you are in the /var/www/html folder before you do this.**:

```
> cd /var/www/html
> sudo cp -R moodle.backup/mod/hotpot moodle/mod/
```

Scroll to the bottom of the page and click on the green `Refresh` link to reload the page. There should no longer be an error with the `hotpot` module.

Let's do this one more time and then I'll let you figure out the rest. Scroll down the page until you see then next plugin that is missing from disk. In this case it is the `multichoiceset` question type plugin:


**Insert multichoiceset_plugin_error**

Note the location of the other `Question type` plugins. They are located at `/question/type`:

**Insert question_type_dir**

Scroll back down the list until you see the `multichoiceset` module that is missing. To copy this plugin over, you need to note down the main folder where it needs to be copied into. In this case it is `/question/type`. The name of the specific plugin folder is whatever you see here:

**Insert proper_plugin_folder_name2**

In this case it is `multichoiceset`. So, here is the full command you need to type. **Make sure that you are in the /var/www/html folder before you do this.**:

```
> cd /var/www/html
> sudo cp -R moodle.backup/question/type/multichoiceset moodle/question/type/
```

Scroll to the bottom of the page and click on the green `Refresh` link to reload the page. There should no longer be an error with the `hotpot` module.

Follow these same steps for every missing plugin until there are no errors.

_Note that the local plugins that are at the bottom of this page can be found in `/local`._

When there are no more plugins missing from disk, click on the grey `Upgrade Moodle database now` button to proceed to the next step:

**Insert upgrade_database_now**

Note that it will take a long time to load the next page.

## Finalizing upgrade

You should now see the `Upgrading to new version` page that will have a list of all of the plugins that have been upgraded/installed:

**Install upgrading_to_new_version**

As long as there are nothing but successful green messages next to all of the plugins in the list, click on the green `Continue` button at the bottom of this page.

You will now be prompted to login as administrator. Please do so now.

The next page will be a list of new settings that are available in Moodle. It is **highly** recommended that you not change these settings now. Instead, take screenshots of every new setting. You can search for these later in the `Site administration` page and decided what course of action to take at that time:

**Install new_settings**

Click on the `Save changes` button at the bottom of this page.

And you are done!
