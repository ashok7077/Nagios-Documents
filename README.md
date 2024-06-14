# Nagios-Documents

# Knowledge Transfer Document for Nagios

## Table of Contents
1. Introduction to Nagios
2. Key Concepts
3. Installation Guide
4. Configuration Guide
5. Monitoring Configuration
6. Notifications
7. Plugins
8. Web Interface
9. Best Practices
10. Troubleshooting
11. Resources

## 1. Introduction to Nagios
Nagios is an open-source monitoring system for computer systems. It watches hosts and services specified, alerting users when things go wrong and when they get better.

### Features
- Monitoring of network services (SMTP, POP3, HTTP, NNTP, PING, etc.)
- Monitoring of host resources (processor load, disk usage, system logs, etc.)
- Simple plugin design that allows users to develop their own service checks
- Parallelized service checks
- Ability to define network host hierarchy using “parent” hosts, allowing detection of network outages
- Contact notifications when service or host problems occur and get resolved
- Escalation of notifications to different contact groups
- Ability to define event handlers to handle problem states
- Web interface for viewing current network status, notification, and problem history, log file, etc.

## 2. Key Concepts

### Hosts and Services
- **Host**: A physical or virtual device on a network.
- **Service**: A service provided by a host (e.g., web server, database server).

### Objects
- **Contacts**: Individuals notified of problems.
- **Commands**: Instructions on what actions to take.
- **Time Periods**: When checks and notifications are performed.
- **Dependencies**: Defines the relationship between services or hosts.

## 3. Installation Guide

### Prerequisites
- A Linux-based operating system (e.g., CentOS, Ubuntu)
- Root privileges

### Steps for CentOS/RHEL
1. **Update the system**:
    ```sh
    sudo yum update -y
    ```

2. **Install required packages**:
    ```sh
    sudo yum install -y httpd php gcc glibc glibc-common wget unzip
    ```

3. **Download and install Nagios**:
    ```sh
    cd /tmp
    wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.x.x.tar.gz
    tar -xvf nagios-4.x.x.tar.gz
    cd nagios-4.x.x
    ./configure
    make all
    sudo make install
    sudo make install-init
    sudo make install-commandmode
    sudo make install-config
    sudo make install-webconf
    ```

4. **Create a Nagios user and group**:
    ```sh
    sudo useradd nagios
    sudo usermod -a -G nagcmd nagios
    ```

5. **Set up web interface authentication**:
    ```sh
    sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
    sudo systemctl restart httpd
    ```

6. **Start Nagios**:
    ```sh
    sudo systemctl start nagios
    sudo systemctl enable nagios
    ```

### Steps for Ubuntu
1. **Update the system**:
    ```sh
    sudo apt-get update
    ```

2. **Install required packages**:
    ```sh
    sudo apt-get install -y apache2 libapache2-mod-php7.0 build-essential libgd-dev
    ```

3. **Download and install Nagios**:
    ```sh
    cd /tmp
    wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.x.x.tar.gz
    tar -xvf nagios-4.x.x.tar.gz
    cd nagios-4.x.x
    ./configure
    make all
    sudo make install
    sudo make install-init
    sudo make install-commandmode
    sudo make install-config
    sudo make install-webconf
    ```

4. **Create a Nagios user and group**:
    ```sh
    sudo useradd nagios
    sudo usermod -a -G nagcmd nagios
    ```

5. **Set up web interface authentication**:
    ```sh
    sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
    sudo systemctl restart apache2
    ```

6. **Start Nagios**:
    ```sh
    sudo systemctl start nagios
    sudo systemctl enable nagios
    ```

## 4. Configuration Guide

### Main Configuration File
- **Location**: `/usr/local/nagios/etc/nagios.cfg`
- This file controls global settings for Nagios.

### Object Configuration Files
- **Location**: `/usr/local/nagios/etc/objects/`
- Contains definitions for hosts, services, contacts, commands, etc.

### Defining a Host
```cfg
define host {
    use                     linux-server
    host_name               hostname
    alias                   Host Alias
    address                 192.168.1.1
    }
```

### Defining a Service
```cfg
define service {
    use                     generic-service
    host_name               hostname
    service_description     HTTP
    check_command           check_http
    }
```

## 5. Monitoring Configuration

### Adding a Host
1. Open the `hosts.cfg` file:
    ```sh
    sudo nano /usr/local/nagios/etc/objects/hosts.cfg
    ```
2. Add the host definition (as shown above).

### Adding a Service
1. Open the `services.cfg` file:
    ```sh
    sudo nano /usr/local/nagios/etc/objects/services.cfg
    ```
2. Add the service definition (as shown above).

### Verify Configuration
```sh
sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
```

### Restart Nagios
```sh
sudo systemctl restart nagios
```

## 6. Notifications

### Defining Contacts
- **Location**: `/usr/local/nagios/etc/objects/contacts.cfg`
- Example:
    ```cfg
    define contact {
        contact_name            nagiosadmin
        use                     generic-contact
        alias                   Nagios Admin
        email                   nagios@yourdomain.com
        }
    ```

### Defining Contact Groups
- **Location**: `/usr/local/nagios/etc/objects/contactgroups.cfg`
- Example:
    ```cfg
    define contactgroup {
        contactgroup_name       admins
        alias                   Nagios Administrators
        members                 nagiosadmin
        }
    ```

## 7. Plugins

### Installing Plugins
1. **Download and extract**:
    ```sh
    cd /tmp
    wget https://nagios-plugins.org/download/nagios-plugins-2.x.x.tar.gz
    tar -xvf nagios-plugins-2.x.x.tar.gz
    cd nagios-plugins-2.x.x
    ```
2. **Compile and install**:
    ```sh
    ./configure --with-nagios-user=nagios --with-nagios-group=nagios
    make
    sudo make install
    ```

### Using Plugins
- Define the check commands in `commands.cfg`:
    ```cfg
    define command {
        command_name    check_http
        command_line    /usr/local/nagios/libexec/check_http -H $HOSTADDRESS$
    }
    ```

## 8. Web Interface

### Accessing the Web Interface
- URL: `http://<your_server_ip>/nagios`
- Login with the username `nagiosadmin` and the password set during installation.

### Customizing the Web Interface
- Modify the web configuration file located at `/usr/local/nagios/etc/objects/templates.cfg`.

## 9. Best Practices

### Use Templates
- Simplifies the configuration by allowing you to define common settings in one place.

### Group Related Services
- Use service groups to organize similar services together.

### Regular Backups
- Regularly back up your Nagios configuration files.

### Monitor Nagios
- Ensure that Nagios itself is monitored for performance and availability.

## 10. Troubleshooting

### Common Issues
- **Configuration Errors**: Use the verification command to identify errors.
    ```sh
    sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
    ```
- **Permissions Issues**: Ensure Nagios user has the appropriate permissions.
- **Service Not Starting**: Check the logs at `/usr/local/nagios/var/nagios.log`.

### Log Files
- **Nagios Log**: `/usr/local/nagios/var/nagios.log`
- **Apache Log**: `/var/log/httpd/error_log` (CentOS/RHEL) or `/var/log/apache2/error.log` (Ubuntu)

## 11. Resources

### Official Documentation
- [Nagios Core Documentation](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/)

### Community
- [Nagios Support Forum](https://support.nagios.com/forum/)

### Books
- **"Learning Nagios 4"** by Wojciech Kocjan

