```
1. Configure the network
a) Assign Hostname and Ip address for your virtual machine.
Hostname serverb.lab.example.com
IP Address 172.25.250.11
Netmask 255.255.255.0
Gateway 172.25.250.254
Nameserver 172.25.250.254



# Change the hostname

# Check the hostname
hostname 

# Add the hostname
sudo hostnamectl set-hostname server.lab.example1.com

# Check the hostname
hostname 

# Add the connection.
nmcli conn modify enp1s0 ipv4.addresses 192.168.122.222/24 ipv4.gateway 192.168.122.1 ipv4.dns 192.168.122.1 ipv4.method manual connection.autoconnect yes

# Now active the connection
nmcli conn up enp1s0 


# Now, check the internet connection.


ping google.com


# Check the details of enpls0

 nmcli conn show enp1s0
 
```



``` 
# 2. Create a repository
# http://content/rhel9.0/x86_64/dvd/AppStream
# http://content/rhel9.0/x86_64/dvd/BaseOS


sudo vim /etc/yum.repos.d/server.repo

[BaseOs]
name= BaseOs
baseurl=http://content/rhel9.0/x86_64/dvd/BaseOS
enabled=1
gpgcheck=0

[AppStream]
name= AppStream
baseurl=http://content/rhel9.0/x86_64/dvd/AppStream
enabled=1
gpgcheck=0


 yum repolist all
 
 yum install telnet
 
 
 ```

 ```

# 3. Configure the Selinux
a) Your webcontent has been configured in port 82 at the /var/www/html directory
(Don't alter or remove any files in this directory) Make the content accessable.


# Install the httpd (Apache) package
yum install httpd

# Enable and start the Apache service
systemctl enable --now httpd

# Check the status of the Apache service
systemctl status httpd

# Display SELinux port type for http
semanage port -l | grep http

# Add a SELinux policy for port 82
semanage port -a -t http_port_t -p tcp 82

# Add http service to firewall (permanent)
firewall-cmd --permanent --add-service=http

# Add port 82 to firewall (permanent)
firewall-cmd --permanent --add-port=82/tcp

# Reload the firewall settings
firewall-cmd --reload

# Verify SELinux port type for http
semanage port -l | grep http

# Change the port 82 
vim /etc/httpd/conf/httpd.conf 

# Restart the Apache service
systemctl restart httpd

# Check the status of the Apache service after restart
systemctl status httpd

```
 
 ```

4. Create the following users, groups and group memberships:
a) A group named admin.
b) A user named Harry who belongs to admin as a secondary group.
c) A user natasha who belongs to admin as a secondary group.
d) A user sarah who does not have access to an interactive shell on the system
and who is not a member of admin.
e) The users harry,natasha,sarah should all have password of password.


# Create a group named admin
groupadd admin

# Create a user named Harry and add to the admin group as a secondary group
useradd -G admin harry

# Create a user named Natasha and add to the admin group as a secondary group
useradd -G admin natasha

# Create a user named Sarah without an interactive shell and not a member of admin
useradd -s /sbin/nologin sarah

# Set passwords for the users (assuming the password is "password" for all)
passwd harry
passwd natasha
passwd sarah

```

```

5. Create a collaborative directory /common/admin with the following characterstics:

a) Group ownership of /common/admin is admin.
b) The directory should be readable,writable and accessible to members of admin, but not to any
other user.
(It is understood that root has access to all files and directories on the system.)
c) Files created in /common/admin automatically have group ownership set to the admin group.

 
 
	
# Create the directory /common/admin
mkdir -p /common/admin

# Set the group ownership of /common/admin to admin
chgrp admin /common/admin

# Check the ownership of /common/admin
ls -ld /common/admin/

# Set the directory permissions to be readable, writable, and accessible to members of the admin group, with the setgid bit
chmod 2770 /common/admin

Here is 2 means set the gid bit.

# Verify the permissions
ls -ld /common/admin



```


```
6. Configure autofs to automount the home directories of production5 domain users.
 Note the
following:
a) servera.lab.example.com (172.25.250.10) NFS -exports /user-homes to your system.
b) production5 home directory is servera.lab.example.com:/user-homes/production5
c) production5 home directory should be automounted locally mapped to /localhome in your system
d) home directories must be writable by their users.
e) While you are able to log in as any of the users production1 through production30,
the only home directory that is accessible from your system is production5.


# Install autofs
yum install autofs

# Enable and start autofs
systemctl enable --now autofs

# Check the status of autofs
systemctl status autofs

# Edit the autofs master configuration file
vim /etc/auto.master

# Add the following line at the end of /etc/auto.master
/localhome /etc/auto.misc
:wq!

# Edit the autofs map file
vim /etc/auto.misc

# Add the following line to /etc/auto.misc
production5 -fstype=nfs,rw servera.lab.example.com:/user-homes/production5

# Save and exit the file

# Restart autofs
systemctl restart autofs

# Check the status of autofs after restarting
systemctl status autofs

```

```
7. Set a Cron job for harry on 12.30 at noon print /bin/echo on "hello".


# Edit harry's crontab
crontab -e -u harry

# Add the following line to schedule the job at 12:30 PM every day
30 12 * * * /bin/echo "hello"

# Save and exit the editor (:wq! for vim)

# View harry's crontab to verify
crontab -l -u harry

```

```
8. Configure the NTP
a) Configure your system so that it is an NTP client of classroom.example.com

# Edit the chrony configuration file
vim /etc/chrony.conf

# Add the following line to specify the NTP server (replace classroom.example.com with the actual NTP server address)
server classroom.example.com iburst

# Save and exit the editor (:wq for vim)

# Restart the chrony service
systemctl restart chronyd.service

# Enable and start the chrony service
systemctl enable --now chronyd.service

# Check the status of the chrony service
systemctl status chronyd.service


```
