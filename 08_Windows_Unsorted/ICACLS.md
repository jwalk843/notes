

# ICACLS
* * *

## ICACLS Syntax

```
icacls <filename> [/grant[:r] <sid>:<perm>[...]] [/deny <sid>:<perm>[...]] [/remove[:g|:d]] <sid>[...]] [/t] [/c] [/l] [/q] [/setintegritylevel <Level>:<policy>[...]]
icacls <directory> [/substitute <sidold> <sidnew> [...]] [/restore <aclfile> [/c] [/l] [/q]]
```

## Examples:

- To save the DACLs for all files in the C:\\Windows directory
    and its subdirectories to the ACLFile file, type:
    `icacls c:\windows\* /save aclfile /t`
    
- To restore the DACLs for every file within ACLFile that exists
    in the C:\\Windows directory and its subdirectories, type:
    `icacls c:\windows\ /restore aclfile`
    
- To grant the user User1 Delete and Write DAC permissions to a
    file named Test1, type:
    `icacls test1 /grant User1:(d,wdac)`
    
- To grant the user defined by SID S-1-1-0 Delete and Write DAC
    permissions to a file, named Test2, type:
    `icacls test2 /grant *S-1-1-0:(d,wdac)`
    

```
ICACLS name /save aclfile [/T] [/C] [/L] [/Q]
    stores the DACLs for the files and folders that match the name
    into aclfile for later use with /restore. Note that SACLs,
    owner, or integrity labels are not saved.

ICACLS directory [/substitute SidOld SidNew [...]] /restore aclfile
                 [/C] [/L] [/Q]
    applies the stored DACLs to files in directory.

    /T executes the operation recursively on all subfolders

    /C forces the operation to continue despite any errors

    /L is used with symbolic links. It causes the operation to be executed on the link itself rather than its target.
```

⧉ Copied!

* * *

## Explanation

Permissions:

```
Like Unix-based permissions, NTFS treats files and directories slightly differently. Let's learn about them one at a time. The following is the basic set of permissions assignable to files. 
```

Read:

```
Allows the user to view the contents of the file. Crucially, read also allows users to execute scripts. 
```

Write:

```
Allows the user to write to the file. Note that write does not allow a user to delete the file, though they can delete its contents. 
```

Read & Execute:

```
Allows the user to read the file, as well as execute binaries. 
```

Modify:

```
Allows the user to read and write to the file. Also allows the user to delete it. 
```

Full Control:

```
Allows the user to read, write, change, or delete the file. 
```

* * *

## Permissions assignable to dirs

Read:

```
Allows the user to view and list any file in the directory. 
```

Write:

```
Allows the user to add files or subfolders to the directory. 
```

Read & Execute:

```
Allows the user to view and list any file or subfolder in the directory,
and also allows execution of any binary within the folder. 
```

List Folder Contents:

```
Allows the user to list any file or subfolder in the directory and also allows
execution of any binary within the folder. List Folder Contents appears at first
glance very similar to Read & Execute. The difference between them is that Read & 
Execute allows for viewing the contents of files as well. 
```

Modify:

```
Allows the user to read, write and delete files and subfolders within the directory. 
```

Full Control:

```
Allows the user to read, write, change, and delete files and subfolders within the directory. Note that the 
```

Full Control permission on a directory allows the user to delete all files in it, even if they aren't assigned Modify or Full Control permissions on the files themselves.

* * *

## Output in command prompt

```
 C:\>icacls

ICACLS name /save aclfile [/T] [/C] [/L] [/Q]
    stores the DACLs for the files and folders that match the name
    into aclfile for later use with /restore. Note that SACLs,
    owner, or integrity labels are not saved.

ICACLS directory [/substitute SidOld SidNew [...]] /restore aclfile
                 [/C] [/L] [/Q]
    applies the stored DACLs to files in directory.
    perm is a permission mask and can be specified in one of two forms:
        a sequence of simple rights:
                N - no access
                F - full access
                M - modify access
                RX - read and execute access
                R - read-only access
                W - write-only access
                D - delete access
        a comma-separated list in parentheses of specific rights:
                DE - delete
                RC - read control
                WDAC - write DAC
                WO - write owner
                S - synchronize
                AS - access system security
                MA - maximum allowed
                GR - generic read
                GW - generic write
                GE - generic execute
                GA - generic all
                RD - read data/list directory
                WD - write data/add file
                AD - append data/add subdirectory
                REA - read extended attributes
                WEA - write extended attributes
                X - execute/traverse
                DC - delete child
                RA - read attributes
                WA - write attributes
        inheritance rights may precede either form and are applied
        only to directories:
                (OI) - object inherit
                (CI) - container inherit
                (IO) - inherit only
                (NP) - don't propagate inherit
                (I) - permission inherited from parent container
```

⧉ Copied!