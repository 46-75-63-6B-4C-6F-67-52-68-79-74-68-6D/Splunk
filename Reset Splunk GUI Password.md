# Reset Splunk GUI Password

<br />

## 2 Methods Can Be Used
*If one does not work, try the other. I have found that sometimes you will need to run through the steps a couple times but it will end up working in the end.*
* Use the `splunk oneliner` to update the password **(Fast Way)**
* Create `user-seed.conf`

<br /><br />

### `Method 1` - Use The Splunk Oneliner To Update The Password
Stop the Splunk service
````
$SPLUNK_HOME/bin/splunk stop
````

<br />

Change the password for the current splunk account. In the below example, I changed/reset the password for the `splunk` user to `newsplunk2023!`
````
$SPLUNK_HOME/bin/splunk edit user splunk -password newsplunk2023! -auth splunk:newsplunk2023!
````

<br />

Start the Splunk service back up
````
$SPLUNK_HOME/bin/splunk stop
````

<br />

![Alt text](https://github.com/46-75-63-6B-4C-6F-67-52-68-79-74-68-6D/Splunk/blob/main/Resources/splunk_password_reset.png "Splunk Password Reset CLI")
*Example output of the CLI command mentioned above.*

<br /><br /><br />

### `Method 2` - Create `user-seed.conf`
  Open the command prompt/terminal of your system. Navigate to the `passwd` file ($SPLUNK_HOME/etc/passwd) of Splunk and rename it to passwd.bk
````
cd /opt/splunk/etc
````
<br />

Rename the `passwd` file
````
mv /opt/splunk/etc/passwd  /opt/splunk/etc/passwd.bk
````

<br />

Create a new .conf file named `user-seed.conf` within $SPLUNK_HOME/etc/system/local
````
cd /opt/splunk/etc/system/local
````
<br />

Create the `user-seed.conf` file
````
touch user-seed.conf
````

<br />

Enter the below contents to the new `user-seed.conf` file
````
[user_info]

USERNAME=admin

PASSWORD=<new_password>
````

<br />

After adding this file restart Splunk to login with your new password specified in `user-seed.conf`
````
$SPLUNK_HOME/bin/splunk restart
````

<br />

*If there are users previously created and they know their own credentials then copy and paste their credentials from the passwd.bk file to new passwd file and then restart Splunk. You have to copy and paste all the credentials of existing users except admin. `$SPLUNK_HOME/bin/splunk restart`*
