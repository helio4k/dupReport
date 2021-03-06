## dupReport.rc File - [main] section

The [main] section contains the high-level program options for dupReport to run properly.

------

**Program Version**

```
version=<major>.<minor>.<subminor>
```

The current version number of the program. Used to determine if the .rc file needs to be updated. **DO NOT** alter the version= option

------

**Database File Path Information**

```
dbpath=<dbpath>
```

 Directory or full path name where the dupReport.db file is located. Can be overridden by the -d command line option.

------

**Log Management**

```
logpath=<logpath> 
```

The directory or full path name where the dupReport.log file is located. Can be overridden by the -l command line option.

```
verbose=<severity level>
```

The settings for the *[main]verbose=* option adhere to the severity levels as defined by the syslog RFC5424:

| *Verbose=* Setting | Severity Level |
| ------------------ | -------------- |
| 0                  | Emergency      |
| 1                  | Alert          |
| 2                  | Critical       |
| 3                  | Error          |
| 4                  | Warning        |
| 5                  | Notice         |
| 6                  | Info           |
| 7                  | Debug          |

dupReport only uses only three of these severity levels during its operation: 

- 0 = No log output
- 5 = General program execution info.
- 7 = Full debugging output

The *[main]verbose=* setting in the .rc file can be overridden by the -v command line option.

```
logappend=false
```

If set to true, append new logs to the existing log file. If set to false, reset log file for each run. Can be overridden by the -a command line option.

```
masksensitive = true
```

Masks sensitive data in the log file. If set to "true" (the default), fields such as user name, password, server name, and file paths will be masked with asterisks in the log file. This is useful for maintaining privacy if the log file needs to be stored or transmitted somewhere else for debugging or analysis purposes. **NOTE**: this setting **<u>will not</u>** mask any information found in the actual emails pulled from your email server, such as sending and receiving email address, server names, etc. It only affects the data that dupReport generates as part of its operation.

------

**Syslog Output**

```
syslog = <server>
```

This option sends the log output to a syslog server or a log aggregator. \<server> can be an IP address or a fully qualified domain name.

You can specify a port on the syslog server by using the format:

```
syslog = <server>:<port>
```

If you do not specify a port, port 514 will be used by default.

```
sysloglevel=5
```

This specifies the logging level used for syslog output. These levels are the same used by the *[main]verbose=* setting described above. The default setting is 5.

If you are sending the syslog output to a log aggregator or SIEM, the following Grok pattern can be used to properly parse the log output:

```
"<%{INT:facil_sev:int}>\[%{TIMESTAMP_ISO8601:ts}\]\[%{DATA:severity_label}\]\[%{DATA:function}\]\[%{DATA:action}\]%{GREEDYDATA:event_message}"
```

------

**Email Message Management**

```
subjectregex=^Duplicati Backup report for
```

A regular expression used to find backup message Emails Of Interest. This should somewhat match the text specified in the ‘send-mail-subject’ advanced option in Duplicati. 

**NOTE**: If you modify the subject line of your Duplicati emails by changing the ‘send-mail-subject’ advanced option in Duplicati, make sure that the subject line you construct ***does not*** use the character you specify as the Source/Destination delimiter in the srcdestdelimiter= option in dupReport.rc (see below). If the subject line uses the same character as the Source/Destination delimiter, dupReport will get confused and not parse your emails properly.

```
srcregex=\w*
```

Regular expression used to find the “source” side of the source-destination pair in the subject line of the email.

```
destregex=\w*
```

Regular expression used to find the “destination” side of the source-destination pair in the subject line of the email.

```
srcdestdelimiter=-
```

Single character used to specify the delimiter between the ‘source’ and ‘destination’ parts of the source-destination pair.

```
dateformat=MM/DD/YYYY
```

Default format for dates found in emails. Acceptable formats are:

- MM/DD/YYYY
- DD/MM/YYYY
- MM-DD-YYYY
- DD-MM-YYYY
- MM.DD.YYYY
- DD.MM.YYYY
- YYYY/MM/DD
- YYYY/DD/MM
- YYYY-MM-DD
- YYYY-DD-MM
- YYYY.MM.DD
- YYYY.DD.MM

If there are problems in your report dates or if you are getting program crashes around the date/time routines, you might try checking and/or changing this value.

The default format can be overridden for specific backup jobs by specifying a dateformat= line in a job-specific section of the .rc file. See [“[source-destination] Sections”](RcFileConfig-SourceDestination.md).

```
timeformat=HH:MM:SS
```

Default format for times found in emails. The only time format currently acceptable is ‘HH:MM:SS’.

```
applyutcoffset=true
```

Duplicati version 2 does not apply local time zone information when reporting start and end times for backup jobs. If set to 'true', this option takes the UTC time zone offset from the email header and applies it to the backup job start and end times. If the backup times in your report do not look right, try adjusting this value.

```
emailservers = incoming, outgoing
```

Specifies the email servers that will be used to retrieve and send emails. See the section on [Email Management](RcFileConfig-IncomingOutgoing.md) for more information on this option.

------

**Special Runtime Options**

```
warnoncollect=false
```

If true, send an email if there are warning or error messages from the Duplicati jobs in the scanned emails. This option only works if the -c ("collect only") command line option is used and allows the user to get error messages even if they are not producing an email report.

```
show24hourtime=false
```

If set to "true," times will be displayed in 24-hour (military) notation. Otherwise, dupReport will use 12-hour (AM/PM) notation.

```
purgedb=true
```

If true, emails in the database that are no longer found on the incoming email server will be purged from the database and the database will be compacted at the end of the program run. 

**NOTE 1:** This option only purges email messages from the dupReport system. It does not affect the status of any messages on the incoming email server. 

**NOTE 2:** Any source-destination pairs referenced in purged emails will remain in the database in case emails for those pairs are seen in the future. To remove obsolete source-destination pair references from the database, use the -m command line option.

```
showprogress=0
```

If this option is greater than zero, dupReport will display a dot ('.') on the system console (stdout) for every 'n' emails that are processed from the incoming server. For example, if 

```
showprogress=5
```

is specified, there will be one '.' for every 5 emails that are read.





(Return to [Main Page](readme.md))