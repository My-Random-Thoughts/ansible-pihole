### Introduction
#### How To Use
- Ensure you have ansible installed on a separate machine (you local machine will be fine)
- Ensure you have added the device you want to install PiHole on has been added to your Ansible hosts file and thast Ansible can communicate with it.
- Execue the first file:
  - `ansible-playbook -l <ttarget-ipaddress> install-pihole-1-base.yaml`
- This will limit the installation to the one device you want to install too
- Once that has completed successfully, you can now log in to the newly installed PiHole instance
- If you want to add the additional configuration, run the second script:
  - `ansible-playbook -l <ttarget-ipaddress> install-pihole-2-additional.yaml`
- Enjoy your newly installed and configured PiHole

#### Ansible YAML Files
There are two main Ansible YAML files, and a third imported one.

##### - `install-pihole-1-base.yaml`
This will install PiHole and Unbound on the target machine and configure it with some custom values.

Once installed PiHole is ready to use and can take either a teleporter backup file for restore,
or some syncing tool if you have more than one PiHole.

##### - `install-pihole-2-additional.yaml`
This will seup a single PiHole instance with additional lsits and other configuration that you specify (shown below).

It can be used to set up a new PiHole from scratch if your only device dies, or you want to move to a new installation.

##### - `list-importer.yaml`
This is used to import the allow/deny lists and should not be used on its own.

### Inital Configuration
#### `install-pihole-1-base.yaml`
##### pihole.toml
The pihole.toml file is the new v6 configuration file that is used for the configuration of PiHole.  For detailed explaination of ALL available settings, have a look at an existing file located in `/etc/pihole/pihole.toml` (default location)

Under the task `set default password`, ensure you set a strong password, or run this command yourself afterwards.  This password used used for the web ui login as well as the API commands called in the second Ansible script

Under the task `create /etc/pihole/pihole.toml`, you only need to put in the non-default values that you have changed into this section

Under the task `create /etc/unbound/unbound.conf.d/pi-hole.conf`, these are the default settings of Unbound.  They should not need to be changed, but you can if you need.

#### `install-pihole-2-additional.yaml`
If you changed the password in the first script (you really should), then ensure the same password is set in this script too.

Under the task `authenticate with api`, change the password as required.


### Configuration Files
#### Allow / Deny Lists
- Contains a list of entries to add to the allow/deny lists
- One entry per file.
- For example `ajax.googleapis.com` or `(\\.|^)lencr\\.org$`.
- Ensure that in the regex files that all special characters are escapped twice.
- Ensure that the files names are exactly as shown below

##### File Names:
- `allow-exact.txt`
- `allow-regex.txt`
- `deny-exact.txt`
- `deny-regex.txt`

#### Additional Lists
##### - `bypass-clients.txt`
- Contains the MAC addresses of each client to add to the `BypassBlocking` group
- One entry per line
- MAC address format should be with colons:  00:00:00:00:00:00

##### - `dns.txt`
- Contains a list of IPs and names for static DNS entries
- One entry per line
- Format is `{IP}%20{NAME}`:  192.168.0.10%20pihole1

##### - `gravity-list.txt`
- Contains a list of URLs to subscribe to for gravity data
- One entry per line
- Format is a fully qualified URL: `https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts`
