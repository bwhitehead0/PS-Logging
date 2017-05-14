# PS-Logging

### Overview

A set of scripts to provide common logging functionality. Logs to both screen buffer and specified logfile.

### Functions

| Function Name | Purpose                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| write-data    | Handles the writing of log data to disk using .NET `System.IO.File`.<br><br>**Parameters:**<br><br>**<u>outputMessage</u>** (required) - Log message<br>**<u>outputFile</u>** (required) - Path to log file                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| get-logtime   | Returns current date in as type string, in format "yyyy-MM-dd HH:mm:ss.fff"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| get-function  | Returns the name of the function writing the log.<br><br>**Parameters:**<br><br>**<u>context</u>** (optional) - If not specified, defaults to `1`. This is used to determine what function is making the log request. Context of 1 will identify the function that called `get-function`. Context of 2 will identify the function that called the function that called `get-function`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| write-log     | The primary function. All other functions are called by this function.<br><br>**Parameters:**<br><br>**<u>logMessage</u>** (required) - Log message.<br>**<u>logTime</u>** (optional) - If not specified, calls `get-logtime` function.<br>**<u>logType</u>** (optional) - Type of log message (error, trace, debug, etc). If not specified, defaults to `0` (Error).<br>**<u>messageContext</u>** (optional) - If not specified, calls `get-function -context 2` to determine calling function. Can be specified to identify other event contexts in log.<br>**<u>logWho</u>** - Uses global variable `$global:executingUser`. Preferred to determine user with `[Security.Principal.WindowsIdentity]::GetCurrent().Name`<br>**<u>path</u>** (optional) - If not specified, uses global variable `$global:logPath`.<br>**<u>file</u>** (optional) - If not specified, uses global variable `$global:logFile`.<br>**<u>silent</u>** (optional) - If not specified, uses global variable `$global:silentOperation`.<br><br>The function also relies on another global variable, `$global:logLevel`, which determines the default behavior of various logs. If a message's `$logType` is less than `$global:logLevel`, it will be written to log, and if `$global:silentOperation` is `$false`, written to the screen buffer. This allows for script debugging and verbose logging by modifying the value of a single variable (`$global:logLevel`). |


### Usage & Examples

`write-log` supports pipeline input for the log message.

Assume the following code block exists elsewhere in the main code:

```Powershell
$global:executingUser=[Security.Principal.WindowsIdentity]::GetCurrent().Name
$global:logPath="D:\log"
$global:logFile="myLog.txt"
$global:silentOperation=$false # Write to screen
$global:logLevel=3 # Debug
```

**Simple log message**

```Powershell
write-log -logMessage "Logging a message."
```

Creates a log entry similar to the following:  
   `2017-05-14 14:55:17.815 mrkurtz - Logging a message.`

**Log an error or success (info)**

```Powershell
try {
  # Do something, like access a file, or read from a DB.
  write-log -logMessage "Successfully read from file/DB." -logType 2
}
catch {
  write-log -logMessage "Error reading from file/DB: $($_.exception.message)"
}
```


















