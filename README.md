## Overview

These tools were written to allow you to add hostnames from your environment into a file called "hostlist" and then add those clients to both a system group and an OS group, as desired.  As long as the file stays seperated into neatly defined colums, you'll be able to call combonations of those groups to deploy commands and files to groups of nodes, as you see fit.
  
## .dshrc file
This file contains a number of variables that you can customize to your liking, should you desire.  The primary field to pay attention to in this file is the `HOSTLIST` variable, which needs to point towards your customized hostlist file on your system.

The `COL` variable defaults to 50 to help support terminals with restricted display sizes, such as AIX consoles and legacy terminal emulators, but can be changed as desired.

Colors can be changed to any ANSI code you'd like, as long as the color names themselves stay the same.  Any alterations to these existing color names or additions of new colors will require you to update/edit the DSH or DCP scripts directly.
```
## COLUMNS ##
COL="50"

## COLORS ##
RED="\e[31m"
LRED="\e[91m"
GREEN="\e[32m"
LIME="\e[92m"
CYAN="\e[36m"
LCYAN="\e[96m"
YELLOW="\e[33m"
LYELLOW="\e[93m"
END="\e[m"

HOSTLIST="/path/to/your/hostlist"
```

  
## hostlist file

Below is an example of a properly formatted hostlist file.  This file is simply named "hostlist" by default, but you can name it anything you like as long as the full path the file is correct specified in your .dshrc file in your home directory.

```  
#DNSName        Owner                OS                System Groups           Description
#--------       -----------          ---------         -------------           -----------------
nodecl01        Linux Team           ,all,rhel,        ,test,                  ,SSH Test Client,
nodecl02        Linux Team           ,all,ubuntu,      ,dev,                   ,SSH Test Client,
nodecl02        Linux Team           ,all,aix,         ,prod,                  ,SSH Test Client,
```
**Note:** You may either add a comment `#` to the beginning of a line or add the word `,off,` to either the OS or Groups column to prevent a client from being included in DSH or DCP calls, if desired.

## DSH Usage

### Description
DSH allows you to issue commands over SSH to multile clients configured in the hostlist file provided with the script.  
For best results, SSH keys should be set up from the issuing server to any clients you add to the hostlist file.

### Options
```
Calling dsh without arguments will provide a short help guide with examples.

-o      specify OS groups to be included in the command as defined in the hostlist file
        entering the word "groups" will provide a list of all OS and System Groups defined in the hostlist file
-g      specify System Groups to be included in the command as defined in the hostlist file
        entering the word "groups" will provide a list of all OS and System Groups defined in the hostlist file
-c      Command to be run against all OS and System Groups specified in the command. This argument can be wrapped in "quotes"
        if needed. Any variables that need to be executed on the locally on the remote client should be escaped with a backslash
        as well as any addition quotes added into the command string.  Any variables that are not escaped are evaluated from the
        environment of the host issuing the DSH command, allowing you to specifiy paths and other variables to the remote host.
-d      output details about the clients specified in the commands in comma-delimted format
```

### Examples
List all clients in the "rhel" OS group:

`dsh -o rhel`

List all clients in the "prod" System Group:

`dsh -g prod`

Execute the `date` command on all RHEL production clients:

`dsh -o rhel -g prod -c "date"`

## DCP Usage

### Description
DSH allows you to copy files from the issuing host over SCP to multile clients configured in the hostlist file provided with the script.  
For best results, SSH keys should be set up from the issuing server to any clients you add to the hostlist file.

### Options
```
Calling dcp without arguments will provide a short help guide with examples.

-o      specify OS groups to be included in the command as defined in the hostlist file
        entering the word "groups" will provide a list of all OS and System Groups defined in the hostlist file
-g      specify System Groups to be included in the command as defined in the hostlist file
        entering the word "groups" will provide a list of all OS and System Groups defined in the hostlist file
-s      Source file to be copied to the specified clients.  File can be in relative or absolute path, and can include
        environment variables local to the issuing host, such as $PWD, if desired.
-d      Destination file location for the specified clients.  File should be an absolute path, but can include 
        environment variables local to the issuing host, such as $PWD, if desired.
-t      Target location for the client.  Exactly the same as -d
        This option exists at the request of a coworker who couldn't remember -d.  Thanks, Clay.
```

### Examples
List all clients in the "rhel" OS group:

`dcp -o rhel`

List all clients in the "prod" System Group:

`dcp -g prod`

copy the `/etc/sudoers` file from the issuing DCP server to all RHEL production clients.

`dcp -o rhel -g prod -s "/etc/sudoers" -d "/etc/sudoers"`

copy the `/etc/sudoers` file from the issuing DCP server to all RHEL production clients, but to the /tmp/ directory.

`dcp -o rhel -g prod -s "/etc/sudoers" -d "/tmp/"`
