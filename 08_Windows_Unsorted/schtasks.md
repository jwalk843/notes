# Windows Scheduled Tasks


```batch
C:\>schtasks /?

SCHTASKS /parameter [arguments]

Description:
    Enables an administrator to create, delete, query, change, run and
    end scheduled tasks on a local or remote system.

Parameter List:
    /Create         Creates a new scheduled task.

    /Delete         Deletes the scheduled task(s).

    /Query          Displays all scheduled tasks.

    /Change         Changes the properties of scheduled task.

    /Run            Runs the scheduled task on demand.

    /End            Stops the currently running scheduled task.

    /ShowSid        Shows the security identifier corresponding to a scheduled task name.

    /?              Displays this help message.

Examples:
    SCHTASKS
    SCHTASKS /?
    SCHTASKS /Run /?
    SCHTASKS /End /?
    SCHTASKS /Create /?
    SCHTASKS /Delete /?
    SCHTASKS /Query  /?
    SCHTASKS /Change /?
    SCHTASKS /ShowSid /?

```

## schtask Examples

```batch
schtasks /query || schtasks
schtasks /create /sc weekly /d mon /tn task-name /tr C: task-name.exe /st 02:22
schtasks /delete /tn task-name
schtasks /query /TN task-name /fo LIST 
```


## Schtask create In depth

### Help Output /create switch

schtasks /create /?

```
SCHTASKS /Create [/S system [/U username [/P [password]]]]
    [/RU username [/RP password]] /SC schedule [/MO modifier] [/D day]
    [/M months] [/I idletime] /TN taskname /TR taskrun [/ST starttime]
    [/RI interval] [ {/ET endtime | /DU duration} [/K] [/XML xmlfile] [/V1]]
    [/SD startdate] [/ED enddate] [/IT | /NP] [/Z] [/F] [/HRESULT] [/?]

Description:
    Enables an administrator to create scheduled tasks on a local or
    remote system.

Parameter List:
    /S   system        Specifies the remote system to connect to. If omitted
                       the system parameter defaults to the local system.

    /U   username      Specifies the user context under which SchTasks.exe
                       should execute.

    /P   [password]    Specifies the password for the given user context.
                       Prompts for input if omitted.

    /RU  username      Specifies the "run as" user account (user context)
                       under which the task runs. For the system account,
                       valid values are "", "NT AUTHORITY\SYSTEM"
                       or "SYSTEM".
                       For v2 tasks, "NT AUTHORITY\LOCALSERVICE" and
                       "NT AUTHORITY\NETWORKSERVICE" are also available as well
                       as the well known SIDs for all three.

    /RP  [password]    Specifies the password for the "run as" user.
                       To prompt for the password, the value must be either
                       "*" or none. This password is ignored for the
                       system account. Must be combined with either /RU or
                       /XML switch.

    /SC   schedule     Specifies the schedule frequency.
                       Valid schedule types: MINUTE, HOURLY, DAILY, WEEKLY,
                       MONTHLY, ONCE, ONSTART, ONLOGON, ONIDLE, ONEVENT.

    /MO   modifier     Refines the schedule type to allow finer control over
                       schedule recurrence. Valid values are listed in the
                       "Modifiers" section below.

    /D    days         Specifies the day of the week to run the task. Valid
                       values: MON, TUE, WED, THU, FRI, SAT, SUN and for
                       MONTHLY schedules 1 - 31 (days of the month).
                       Wildcard "*" specifies all days.

    /M    months       Specifies month(s) of the year. Defaults to the first
                       day of the month. Valid values: JAN, FEB, MAR, APR,
                       MAY, JUN, JUL, AUG, SEP, OCT, NOV, DEC. Wildcard "*"
                       specifies all months.

    /I    idletime     Specifies the amount of idle time to wait before
                       running a scheduled ONIDLE task.
                       Valid range: 1 - 999 minutes.

    /TN   taskname     Specifies the string in the form of path\name
                       which uniquely identifies this scheduled task.

    /TR   taskrun      Specifies the path and file name of the program to be
                       run at the scheduled time.
                       Example: C:\windows\system32\calc.exe

    /ST   starttime    Specifies the start time to run the task. The time
                       format is HH:mm (24 hour time) for example, 14:30 for
                       2:30 PM. Defaults to current time if /ST is not
                       specified.  This option is required with /SC ONCE.

    /RI   interval     Specifies the repetition interval in minutes. This is
                       not applicable for schedule types: MINUTE, HOURLY,
                       ONSTART, ONLOGON, ONIDLE, ONEVENT.
                       Valid range: 1 - 599940 minutes.
                       If either /ET or /DU is specified, then it defaults to
                       10 minutes.

    /ET   endtime      Specifies the end time to run the task. The time format
                       is HH:mm (24 hour time) for example, 14:50 for 2:50 PM.
                       This is not applicable for schedule types: ONSTART,
                       ONLOGON, ONIDLE, ONEVENT.

    /DU   duration     Specifies the duration to run the task. The time
                       format is HH:mm. This is not applicable with /ET and
                       for schedule types: ONSTART, ONLOGON, ONIDLE, ONEVENT.
                       For /V1 tasks, if /RI is specified, duration defaults
                       to 1 hour.

    /K                 Terminates the task at the endtime or duration time.
                       This is not applicable for schedule types: ONSTART,
                       ONLOGON, ONIDLE, ONEVENT. Either /ET or /DU must be
                       specified.

    /SD   startdate    Specifies the first date on which the task runs. The
                       format is yyyy/mm/dd. Defaults to the current
                       date. This is not applicable for schedule types: ONCE,
                       ONSTART, ONLOGON, ONIDLE, ONEVENT.

    /ED   enddate      Specifies the last date when the task should run. The
                       format is yyyy/mm/dd. This is not applicable for
                       schedule types: ONCE, ONSTART, ONLOGON, ONIDLE, ONEVENT.

    /EC   ChannelName  Specifies the event channel for OnEvent triggers.

    /IT                Enables the task to run interactively only if the /RU
                       user is currently logged on at the time the job runs.
                       This task runs only if the user is logged in.

    /NP                No password is stored.  The task runs non-interactively
                       as the given user.  Only local resources are available.

    /Z                 Marks the task for deletion after its final run.

    /XML  xmlfile      Creates a task from the task XML specified in a file.
                       Can be combined with /RU and /RP switches, or with /RP
                       alone, when task XML already contains the principal.

    /V1                Creates a task visible to pre-Vista platforms.
                       Not compatible with /XML.

    /F                 Forcefully creates the task and suppresses warnings if
                       the specified task already exists.

    /RL   level        Sets the Run Level for the job. Valid values are
                       LIMITED and HIGHEST. The default is LIMITED.

    /DELAY delaytime   Specifies the wait time to delay the running of the
                       task after the trigger is fired.  The time format is
                       mmmm:ss.  This option is only valid for schedule types
                       ONSTART, ONLOGON, ONEVENT.

    /HRESULT           For better diagnosability, the process exit code
                       will be in the HRESULT format.

    /?                 Displays this help message.

Modifiers: Valid values for the /MO switch per schedule type:
    MINUTE:  1 - 1439 minutes.
    HOURLY:  1 - 23 hours.
    DAILY:   1 - 365 days.
    WEEKLY:  weeks 1 - 52.
    ONCE:    No modifiers.
    ONSTART: No modifiers.
    ONLOGON: No modifiers.
    ONIDLE:  No modifiers.
    MONTHLY: 1 - 12, or
             FIRST, SECOND, THIRD, FOURTH, LAST, LASTDAY.

    ONEVENT:  XPath event query string.
```


