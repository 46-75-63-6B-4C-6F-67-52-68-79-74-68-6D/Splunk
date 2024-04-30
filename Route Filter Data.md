## Route and Filter Data <br />
*References:* https://docs.splunk.com/Documentation/Splunk/9.2.0/Forwarding/Routeandfilterdatad 
<br /><br />
Only heavy forwarders can route or filter all data based on events. Universal and light forwarders cannot inspect individual events except in the case of extracting fields with structured data. They can still forward data based on a host, source, or source type.

<br />

## NullQueue

Example props & transforms for routing a specific subset of data to the nullQueue:

````
-Props.conf
[sourcetype-name]
TRANSFORMS-null= fgt_filter
````

````
-Transforms
[fgt_filter]
REGEX = \ssubtype\=(?<fgtdrop>\"ssl|objcfg|\"voip|fgd|\"anomaly|system|logfile|dm|dvm|scply|malware|fazsys|logging|report|logdb)
DEST_KEY = queue
FORMAT = nullQueue
````

<br />

## Routing Data to a Specific Index

Example props & transforms for routing a specific subset of data to a specified index:
````
-Props
[sourcetype-name]
TRANSFORMS-null= fgt_route1
````

````
-Transforms
[fgt_route1]
DEST_KEY = _MetaData:Index
REGEX = initiatedBy\"\: \{.*?\"user\"\: \{.*? \"userPrincipalName\"\: .*?@(?i:user.com|user1.com.cn|user1.com|company.com|company.com)\",
FORMAT = index_name
````
<br /><br />
## Keeping specific events and discard the rest

Keeping only some events and discarding the rest requires two transforms. In this scenario, which is opposite of the previous, the setnull transform routes all events to nullQueue while the setparsing transform selects the sshd events and sends them on to indexQueue.

As with other index-time field extractions, processing of transforms happens in the order that you specify them, from left to right. The key difference is the order in which you specify the stanzas. In this example, the setnull stanza must appear first in the list. This is because if you set it last, it matches all events and sends them to the nullQueue, and as it is the last transform, it effectively throws all of the events away, even those that previously matched the setparsing stanza.

When you set the setnull transform first, it matches all events and tags them to be sent to the nullQueue. The setparsing transform then follows, and tags events that match [sshd] to go to the indexQueue. The result is that the events that contain [sshd] get passed on, while all other events get dropped.

See below example of props and transforms to filter some events to an index and then discard the rest:


`````
-Props.conf
[source::/var/log/messages]
TRANSFORMS-set= setnull,setparsing
`````

````
-Transforms.conf
[setnull]
REGEX = .
DEST_KEY = queue
FORMAT = nullQueue

[setparsing]
DEST_KEY = _MetaData:Index
REGEX = \[sshd\]
FORMAT = index-name
````

<br />
