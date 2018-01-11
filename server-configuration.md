# Server Configuration

Configuration in CoD4x happens, like in all q3 based game in a global variable store. The variables can be most easily accessed and explored from within the server console.

> Example on a Linux server:  
> Just run your server. Type "sv\_hostname" on the console, and hit return. The name of your server will be shown.

To make configuration comfortable and persistent configuration files are used. The basic configuration is named q3config.cfg and resides in the main folder. Configuration \(.cfg\) files are simple text files containing one entry by line.

```
set sv_hostname "My cool testserver"
gamemode war                            // this is a comment
set sv_maprotation "map mp_killhouse"   // because i like killhouse 

```

> Additional config files can be execute by adding +exec "config.cfg" as a commandline parameter to the server



