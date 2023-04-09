# stash-zabbix-integration
Get statistics for Stash instances within Zabbix.

The Zabbix agent should be installed on the machine running Stash in order for this to work. This only works on Linux machines, and only Ubuntu 22.04 has been tested. The code is probably a bloody mess, and I'm probably breaking quite a few cardinal sins with my approach, so be warned. I just wanted to share this in case it helps anybody out.

# Install dependency command 'stashstat'
'stashstat' is a very basic command that returns values from the Stash database based on the argument passed to it.

Command examples:
| Command  | Returned value |
| ------------- | ------------- |
| stashstat performer  | The number of performers present in the Stash database.  |
| stashstat scene | The number of scenes present in the Stash database.  |
| stashstat studio | The number of studios present in the Stash database.  |
| stashstat withid | The number of scenes present in the Stash database with a Stash ID set.  |
| stashstat withoutid | The number of scenes present in the Stash database without a Stash ID set.  |
| stashstat organized | The number of scenes present in the Stash database that are marked as 'organized'.  |
| stashstat unorganized | The number of scenes present in the Stash database that are not marked as 'organized'.  |

## Installation
Edit 'stashstat' and replace the STASHDBPATH variable with the path to your Stash SQLite database. Copy 'stashstat' to your /usr/local/bin folder. Make the file executable by running this command:
```
chmod +x /usr/local/bin/stashstat
```

# Allow command to be run by Zabbix
Add the following line to your Zabbix agent configuration (by default, this is located at /etc/zabbix/zabbix_agentd.conf, or /etc/zabbix/zabbix_agent2.conf if you are using Zabbix Agent 2):
```
UserParameter=stash.statistics[*], /usr/local/bin/stashstat $1
```

The Stash database file need to be accessible by the 'zabbix' user. This can be done by modifying the groups/permissions on the Stash SQLite database file to allow the Zabbix user access to this file. Alternatively, if you want to live dangerously, you can make it run as sudo (this is probably a very bad idea, but I'm lazy, so this is what I do):
```
UserParameter=stash.statistics[*], sudo /usr/local/bin/stashstat $1
```

Restart your Zabbix agent by running this command:
(For Zabbix Agent)
```sudo systemctl restart zabbix-agent```

(For Zabbix Agent 2)
```sudo systemctl restart zabbix-agent2```
