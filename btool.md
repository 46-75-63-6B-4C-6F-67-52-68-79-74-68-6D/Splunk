 ### Useful btool commands

 <br />List configurations of outputs.conf via btool (Where is the forwarder sending data to?)
 ```
$SPLUNK_HOME splunk btool outputs list –debug
```

 <br />List configurations of deploymentclient.conf via btool (Is the server being managed by a Deployment Server?)
 ```
$SPLUNK_HOME splunk btool deploymentclient list –debug
```

<br />List SSL cert configuration
````
$SPLUNK_HOME/bin/splunk cmd btool server list sslConfig --debug
````

<br />Locate frozen data directory paths:
````
/opt/splunk/bin/splunk btool indexes list --debug | grep coldToFrozenDir
````

<br />
