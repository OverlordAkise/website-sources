# Linux server setup

I recommend you to buy a linux server instead of renting a gmodserver directly.

It is cheaper, you can install way more services on your linux server (webserver, database, teamspeak, forum, etc.) and you learn a lot from it.

**IMPORTANT: NEVER RUN A GAMESERVER AS THE ROOT USER!!**

It is considered insecure and very bad practice to run any service as the root user. Always create a new user for a new application.



## Initial configuration

*WARNING*: All the following commands expect you to have a Debian or Ubuntu system and that you are currently logged in as the root user.


### Brute force protection

After receiving your login to your linux server first install fail2ban to automatically ban wrong logins via ssh.

    apt-get install fail2ban


### Performance monitoring

To automatically monitor your CPU usage you can install the `sar` tool with

    apt-get install sysstat

Afterwards edit the the following file

    /etc/default/sysstat

and change the `false` to `true`.  
Then restart the service with

    systemctl restart sysstat

Now, after waiting 10 minutes, you can use the command

    sar

to display CPU usage, iowait times and %steal.

### User setup

Always create new users for new gameserver / services.

*WARNING*: You should **NEVER** give anyone the password to the root user. You only give out the login details for these gameserver users.

For example, if you want a darkrp server create a darkrp user with

    adduser darkrp

This automatically creates the home directory (in this case `/home/darkrp`) and lets the user login via ssh.  
You can then use this user to install a LinuxGSM gmodserver with the darkrp gamemode running on it.

If you want to have multiple gameservers on one machine I highly recommend to change the readability of your other users in your home directory to be more restrictive.

If you have 2 users, "darkrp" and "sandbox", then they should NOT see each others files. If a user had access to the darkrp user this could mean he could see all files of the sandbox user and could steal them.

To make a home directory not readable except by the user itself you can use the `chmod` command.

To make the `darkrp` users home folder only readable for himself use the following command

    chmod -R o-rwx /home/darkrp

This recursively (-R) changes the permission for others (o) so that the read,write,execute (rwx) rights get removed (-).

The `/root` folder of the root user is never readable by any other user. This is why I always backup gameservers as the root user, because the backups are then save from any other user on the system.



## Paranoid configuration

The following is not required for a secure server but recommended.

### Disabling root login

After first logging into your server I highly recommend to disable root login.

*Why?*: The root user exists on every system and is thus always a target for hacking attacks. Most automated brute force attacks on your server will be on port 22 against the root user. Right now as you read this someone is probably trying to login as root on your server.

To still be able to administrate your server we will create a root-like user that can do admin commands via the `sudo` command, which means "superuser do".

First install sudo with

    apt-get install sudo

Then create your new root-user (we call it `debian`) with

    adduser debian

Then add him to the sudo group with

    usermod -aG sudo debian

And finally edit your sshd config file to disable root login.  
First enter

    nano /etc/ssh/sshd_config

And then change the following line to say `no`

    PermitRootLogin no


### Firewall

Normally it is advised to have a firewall running to secure local-only services from the internet.

I tend to use `iptables` for my firewall configuration.  
Alternatives are `ufw` and `firewalld`.

An example for an iptables config, having ssh, a webserver and a gmodserver running:

    # sample configuration for iptables service
    # from shira.at
    *filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 27015 -j ACCEPT
    -A INPUT -p udp -m state --state NEW -m udp --dport 27015 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 27005 -j ACCEPT
    -A INPUT -p udp -m state --state NEW -m udp --dport 27005 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 27020 -j ACCEPT
    -A INPUT -p udp -m state --state NEW -m udp --dport 27020 -j ACCEPT
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    COMMIT


The above config only allows traffic to ssh (22), http (80), https(443) and the gmod server (27015,27005,27020). It `REJECT`s all the other network packages it receives on any other port.

As you can see we don't open the port 3306 (mariadb) to the public. We use the mysql database only for our garrysmod server internally (on the same server), which means it doesn't have to be accessible from the internet. Now it is impossible for someone to try and connect to it outside of this server, making it more secure against possible exploits.
