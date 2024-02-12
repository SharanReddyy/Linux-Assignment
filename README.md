# Linux-Assignment
# 1. Configuring SMTP in localhost

### 1. Firstly, we need to make sure to have a user with sudo privileges in Linux system.

### 2. Check and update the server using

               sudo apt update 

### 3. Mail servers use Simple Mail Transfer Protocol (SMTP) to send, receive and relay outgoing email messages between senders and recipients. We are going to use Postfix, a mail transfer agent to configure SMTP suing the following command.
           
               sudo apt install postfix
 
### 4. If the installation is completed as expected, it will show a configuration window and we need to choose the postfix configuration. We can choose from No Configuration, Internet Site, Internet with Smart Host, Satellite syste and local only.

#### We are going to choose internet site because we aren’t dependent on another smart host and also want to receive mails from internet. 

### 5. The next step is to make the required changes to the postfix configuration file.

#### The postfix configuration file called main.cf is stored in the postfix directory inside /etc directory. It can be edited using the command:

                sudo nano /etc/postfix/main.cf 

#### It is also recommended to make a backup of the configuration file, in case it is required for restoration.

#### A copy can be made in the same location using the command:
                 
                 sudo cp /etc/postfix/main.cf /etc/postfix/main.cf.original

#### Running the above command creates a backup file called main.cf.original for main.cf in the same location. Any desired name can be used for the backup file but it is recommended to use .original or .backup for easy identification.

### 6. In the main.cf file, make changes/add the following lines:

#### Change relayhost = to relayhost = [smtp.gmail.com]:587. Here we are instructing postfix to use  Gmail’s SMTP server for all  non-local mails and 587 is the port number.

#### To enable SMTP to authenticate itself to the remote SMTP server, here Gmail server. 
                
                 smtp_sasl_auth_enable = yes

#### We also provide credentials like Email ID and App Password to help SMTP authenticate by creating a file called sasl_passwd in the sasl directory inside postfix directory which is located in /etc. The file contains Gmail’s SMTP hostname, port number, Email ID and password in the folloing format:
                 [smtp.gmail.com]:587 example@gmail.com:<app passowrd>
#### To specify that anonymous authentication is not allowed and credentials are mandatory for authentication:

                 smtp_sasl_security_options = noanonymous

#### Now that credentials are mandatory, we need to mention the path to access the credentials. In our case the path is:

                 smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd

#### To instruct postfix to use opportunistic encryption when using remote server. Postfic will encrypt if possible or proceed without encryption.                 

                 smtp_tls_security_level = encrypt

#### To point to the location of the Certificate Authority file

                 smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

#### This is the default location of CA file in most Linux systems and is important for encryption. Both client and server have these certificates for authentication to prevent any unwanted attacks.

### 7. Once the required changes are made in the main.cf file, the changes have to be saved. All the changes can be applied by starting the postfix server by using the command:
 
                  sudo systemctl enable postfix 

#### followed by the command:

                  sudo systemctl start postfix 


### 8. The status of the server can be checked using the command:

                  sudo systemctl status postfix

### 9. On confirming that the status of postfix is active, it can be tested if it is working as intended. The following command helps to test it:

                  sudo telnet <ip address of the linux machine which is expected to run SMTP>

### 10. If we see that the localhost is successfully connected, we can proceed to install the mail-sending packages using:

                   sudo apt install bsd-mailx 

### 11. To make sure the firewall doesn’t block mails, we can run the following commands:

#### To configure the Uncomplicated Firewall (UFW) to allow incoming network traffic on the port associated with the Postfix mail server:

                   sudo ufw allow “Postfix”

#### To configure the Uncomplicated Firewall (UFW) to allow incoming network traffic on the port associated with the Postfix mail server when operating with SMTPS specifically:

                     sudo ufw allow “Postfix SMTPS”

#### To configure the Uncomplicated Firewall (UFW) to allow incoming network traffic on the port associated with the Postfix mail server when operating with submission protocol:

                     sudo ufw allow “Postfix Submission”

### 12. Now that the postfix server and firewall are configured the send mail command can be tested using the following comand:

                     sendmail <recipient email address>
                     Subject: <Desired subject>
                     <Desired body of the mail>
                     .<enter key to exit>

### 13. Here is a snippet of email sent using sendmail sent from shreddsig@gmail.com to sai.sharan@sigmoidanalytics.com. The name Hii T460 can also be changed to a desired name by editing the hostname.

![image](https://github.com/SharanReddyy/Linux-Assignment/assets/78129496/043b4bfb-da22-45ee-979c-278d6ca16908)


*Sent from a newly created Email ID “shreddsig@gmail.com”



## 2. Create a new user who cannot run sudo command:

### 1. Create a new user using the command:

                     sudo adduser <Desired Username> 

### 2. Make sure the newuser is not listed in the users with root access in the /etc/sudoers file. Use visudo to edit or view the file.

### 3. Check the groups which have root or sudo user access and make sure the new user doesn’t belong to any of the groups with sudo or root access. The following command can be used to check all the groups a user belongs to:

                     groups <username>
### 4. By default, the user is not in the sudo group. If he is, use the following command to delete:

                     sudo deluser <username> sudo

## 3. Configuring the system in such a way that running the describe command lists all the contents in the present directory.

### 1. Originally the command taht prints all the contents of directory in “ls”.

### 2. Now we want a command called Describe to perform the same actions. So we assign the same functionality of the ls  command to a new command called describe.

### 3. For this we can use alias. The following is the syntax:

                     alias describe=’ls’

### 4. The alias is only local for this user and doesn’t work anymore after closing the session. For it to work after the session or in other sessions, add the alias describe=’ls’ to .bashrc file using the vi .bashrc command.

### 5. For the alias to work globally for all users, the alias has to be added to the .bashrc in root user.


## 4. Finding a compressed file and its compression type and uncompressing it.

### 1. First we need to search and find the location of the compressed file using the command:

                     file_found=$(find / -type f -name "research.*"  | head -n 1)

#### Here, we use the find command to find a file which is specified using -type f. The name of the file we are searching for is specified in “ “. Here we are searching for the file named research and the .* means any extension after the file name research. After the search, the results are piped and the head -n 1 command is used meaning we will only use the first file found with the name research if there are more than one.

### 2. Check if the variable file_found is empty and if it is not empty, it means a file called research is found. The following command can be used to find out if the variable is empty or not.

                     if [ -n "$file_found" ]; then

#### If the consition is true, we proceed to the next step or directly skip to the else command and print no such file is found. This condition will be described after the if is true condition.

### 3. If a file is found, the next step is to find out the compression type. This can be done using the command:

                     compression_type=$(file -b "$file_found" | awk '{print $2}')

#### Here, we first create a variable called compression_type to store the type of compression. Then the file command is used to find the file type of the mentioned variable “file_path” where we previuosly stored the file path for research. The -b specifies a brief output. The file comand provides more information than we need so we pipe the output to and run the awk comand to get only the second field which contains the file comprssion type. 

### 4. Now we found the file and also the compression type, the next step is to uncompress the file. The switch case would be a good option to choose from the list of compression types and uncompress the file. We can run the followinf script to finally uncompress the file.

    case $compression_type in
        "Zip")
            echo "The file found is: $file_found"
            echo "The compression type is: $compression_type"
            unzip "$file_path"
            echo "File successfully uncompressed."
            ;;
        "gzip")
            echo “The file found is: $file_found"
            echo "The compression type is: $compression_type"
            gunzip "$file_path"
            echo "File successfully uncompressed."
            ;;
        *)
            echo "Unsupported compression type: $compression_type"
            ;;
    esac
else
    echo "No file called “research” found."
fi

#### Here, the compression type is passed and if it is .zip then it is unzipped or gunzipped if it is gzipped depending on the compression type. The script also prints the name of the file, compression type before uncompressing and prints “File successfully uncompressed” after uncompressing. If no file called research is found, it will print “No file called research found”. If any compression type apart from zip and gzip is passed, it prints “Unsupported compression type” and also the compression type. We can define more cases for more compression types on will.

## 5. Configuring the system in such a way that when any user creates a new file, there should not be permission to do any activity on the file.

### 1. We use the umask command that is used to set the default permissions for newly created files.The value specified with umask is subtracted from the maximum possible permissions to determine default permissions. We can do this by using the following command in .bashrc file:
 
                     umask 077

### 2. For this to be implemented globally we need to use make changes to the Access Control List too using the acl packages.

## 6. Creating a service showtime that writes time into a file every minute.

### 1. The first step is to create a bash script. It can be named showtime.sh and recommended to be created in user home directory.

####Bash script:

#!/bin/bash

while true; do
    date '+%Y-%m-%d %H:%M:%S' >> "$HOME/showtime_log.txt"
    sleep 60
done

#### In this script, #!/bin/bash is the chosen interpreter or shell.

#### The while condition is always true and executes indefinitely. 

#### The date command print the date in YYYY-MM-DD HH-MM-SS format into the file showtime_log.txt inside the user’s home directory.

#### The sleep command instructs the script to pause for 60 seconds so that the script prints the time into showlog file only once every minute. 

### 2. Enable access for executing the showtime.sh file. 

                     sudo chmod +x /home/hii/showtime.sh

### 3. Create a service that points to the showtime.sh file to start running. Create a showtime.service file in /etc/systemd/system directory and write in the followig lines:

#### This describes the services in a way that is understandable by humans:
                     [Unit]
                     Description=Showtime Service

#### This defines that the service is a simple service:

                     [Service]
                     Type=simple

#### This line points to the bash script that should run when service is started:

                     ExecStart=/home/hii/showtime.sh

#### The following lines restarts the service in case of any type of failure:

                     Restart=always


### 4. Now that the service is created and ready, we can run the following commands to enable and start the service:

                     sudo systemctl enable showtime.service
                     sudo systemctl start showtime.service

### 5. Now check the status of the service by using command:
 
                     sudo systectl status showtime.service 

### 6. Ideally the status should be active and when the status is active cat the showtime_log.txt to check if the service is printing the time into the file every one minute like this:

![image](https://github.com/SharanReddyy/Linux-Assignment/assets/78129496/51d9c2bf-4fb6-419a-adc4-4e498f5ce951)





                     

 
