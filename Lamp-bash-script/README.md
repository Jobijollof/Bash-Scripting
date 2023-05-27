
# Automate the Implementation of  a Lampstack on AWS with bash script

## Steps:

- Provision an EC2 instance on AWS

- Open port 80 and  22 in security group inbound rules

- In the root directory create a file and name it lamp_setup.sh

```

touch lamp_setup.sh
sudo nano lamp_setup.sh

```

- Add the following  script into the file

```

#!/bin/bash

# Update system packages
sudo apt update
sudo apt upgrade -y

# Install Apache
sudo apt install apache2 -y

# Start and enable Apache service
sudo systemctl start apache2
sudo systemctl enable apache2

# Install MySQL Server
sudo apt install mysql-server -y

# Start and enable MySQL service
sudo systemctl start mysql
sudo systemctl enable mysql

# Secure MySQL installation
SECURE_MYSQL=$(expect -c "
set timeout 10
spawn sudo mysql_secure_installation
sleep 1
expect \"Enter password for user root:\"
send \"jollof1\n\"
expect \"Change the password for root ?\"
send \"n\n\"
expect \"Remove anonymous users?\"
send \"Y\n\"
expect \"Disallow root login remotely?\"
send \"Y\n\"
expect \"Remove test database and access to it?\"
send \"Y\n\"
expect \"Reload privilege tables now?\"
send \"Y\n\"
expect eof
")

# Display completion message
echo "MySQL server secured!"

# Install PHP and required modules
sudo apt install php libapache2-mod-php php-mysql -y

# Restart Apache to load PHP module
sudo systemctl restart apache2

# Display PHP version
php -v

# Display Apache version
apache2 -v

# Display MySQL version
mysql --version

# Test PHP setup
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php

# Display completion message
echo "LAMP stack setup completed!"

```

![str](./images/mysql-secured.png)


- `The SECURE_MYSQL=$(expect -c "` line of code runs the mysql_secure_installation script and stores the output in the SECURE_MYSQLvariable. The `mysql_secure_installation` script will secure the MySQL installation by setting a password for the root user, removing anonymous users, disallowing root login remotely, and removing the test database.

- The `set timeout 10` line sets the timeout for the expect command to 10 seconds. This means that if the expect command does not receive a response within 10 seconds, it will exit.

- The `spawn sudo mysql_secure_installation` line starts the mysql_secure_installation script as the root user.

- `The sleep 1` line pauses the script for 1 second. This is done to give the `mysql_secure_installation` script time to start.

- The `expect "Enter password for user root:"` line expects the user to enter the password for the root user.

- The send \"jollof1\n\" line sends the password "jollof1" to the mysql_secure_installation script.

- The `expect "Change the password for root ?"``` line expects the user to answer "no" to the question "Change the password for root?".

- The send \"n\n\" line sends the answer "no" to the mysql_secure_installation script.

- The `expect "Remove anonymous users?"``` line expects the user to answer "yes" to the question "Remove anonymous users?".

- The send \"Y\n\" line sends the answer "yes" to the mysql_secure_installation script.

- The `expect "Disallow root login remotely?"``` line expects the user to answer "yes" to the question "Disallow root login remotely?".

- The send \"Y\n\" line sends the answer "yes" to the mysql_secure_installation script.

- The `expect "Remove test database and access to it?"``` line expects the user to answer "yes" to the question "Remove test database and access to it?".

- The send \"Y\n\" line sends the answer "yes" to the mysql_secure_installation script.

- The `expect "Reload privilege tables now?" line expects the user to answer "yes" to the question "Reload privilege tables now?".

- The send \"Y\n\" line sends the answer "yes" to the mysql_secure_installation script.

- The expect eof line expects the mysql_secure_installation script to exit.

Once the mysql_secure_installation script has exited, the value of the SECURE_MYSQL variable will be set to the output of the script. The output of the script will contain the new password for the root user, and the status of the other security changes that were made.


- This method also sets up a Lamp stack, but there are still some manual operations at the `Mysql_secure_installation`

```

#!/bin/bash

# Update system packages
sudo apt update
sudo apt upgrade -y

# Install Apache
sudo apt install apache2 -y

# Start and enable Apache service
sudo systemctl start apache2
sudo systemctl enable apache2

# Install MySQL Server
sudo apt install mysql-server -y

# Start and enable MySQL service
sudo systemctl start mysql
sudo systemctl enable mysql

# Run mysql_secure_installation script
sudo mysql_secure_installation

# Set the root password for MySQL
echo "ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'jollof1';" | sudo mysql -u root

# Restart MySQL service
sudo systemctl restart mysql

# Display MySQL version
mysql --version

# Display completion message
echo "MySQL server setup completed!"

# Install PHP and required modules
sudo apt install php libapache2-mod-php php-mysql -y

# Restart Apache to load PHP module
sudo systemctl restart apache2

# Display PHP version
php -v

# Display Apache version
apache2 -v

# Display MySQL version
mysql --version

#Test PHP setup
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php

# Display completion message
echo "LAMP stack setup completed!"

```



- grant the file permission and run

`chmod u+x lamp_setup.sh`

- Run the script

`sudo ./lamp_setup.sh`


- Run mysql_secure_installation script:

This part of the code is going to prompt you for some configurations

![config](./images/mysql.png)


- Set root password? [Y/n]:

This prompt asks if you want to set a password for the MySQL root user. You can press "Enter" to proceed with setting a password or choose "n" if you don't want to set a password at this time.

- Remove anonymous users? [Y/n]:

This prompt asks if you want to remove any anonymous user accounts in MySQL. It is generally recommended to remove anonymous users for security purposes. Press "Enter" to proceed with removing anonymous users or choose "n" if you want to keep them.

- Disallow root login remotely? [Y/n]:

This prompt asks if you want to disable remote login for the MySQL root user. It is advisable to disable remote root login for improved security. Press "Enter" to proceed with disabling remote root login or choose "n" if you want to allow it.
Remove test database and access to it? [Y/n]:

This prompt asks if you want to remove the test database and revoke access to it. The test database is usually not needed in production environments and can pose a security risk. Press "Enter" to proceed with removing the test database and access or choose "n" if you want to keep it.

- Reload privilege tables now? [Y/n]:

This prompt asks if you want to reload the privilege tables to apply the changes made during the configuration process. Press "Enter" to proceed with reloading the privilege tables or choose "n" if you prefer to do it later manually.
The purpose of the mysql_secure_installation script is to help you secure your MySQL installation by addressing common security concerns. It guides you through the process of setting a root password, removing unnecessary accounts and databases, and disabling remote root login.

![root](./images/completed.png)

- To confirm installation use the Public Ip of webserver:80

![apache](./images/apache.png)

- Check for mysql and php installation

![end](./images/version.png)