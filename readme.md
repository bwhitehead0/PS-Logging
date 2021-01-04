# PS-Logging

### Overview

A set of scripts to provide common logging functionality. Logs to both screen buffer and specified logfile.

### Functions


<table border="1" style="border-collapse: collapse; border-style:solid;">
	<tr>
		<td width="200" style="border: 1px solid black;"><strong>Function Name</strong></td>
		<td align="center" style="border: 1px solid black;"><strong>Purpose</strong></td>
	</tr>
	<tr>
		<td align="left" style="border: 1px solid black;">write-data</td>
		<td align="left" style="border: 1px solid black;">Handles the writing of log data to disk using .NET <code>System.IO.File</code>.
			<br>
			<br>
			<strong>Parameters:</strong>
			<br>
			<br>
			<strong style="text-decoration: underline;">outputMessage</strong> (required) - Log message
			<br>
			<strong style="text-decoration: underline;">outputFile</strong> (required) - Path to log file</td>
	</tr>
	<tr>
		<td align="left" style="border: 1px solid black;">get-logtime</td>
		<td align="left" style="border: 1px solid black;">Returns current date as type string, in format <pre>yyyy-MM-dd HH:mm:ss.fff</pre></td>
	</tr>
	<tr>
		<td align="left" style="border: 1px solid black;">get-function</td>
		<td align="left" style="border: 1px solid black;">Returns the name of the function writing the log.
			<br>
			<br>
			<strong>Parameters:</strong>
			<br>
			<br>
			<strong style="text-decoration: underline;">context</strong> (optional) - If not specified, defaults to <code>1</code>. This is used to determine what function is making the log request. Context of <code>1</code> will identify the function that called get-function. Context of <code>2</code> will identify the function that called the function that called <code>get-function</code>. If not specified, the function <code>write-log</code> will use <code>-context 2</code>.</td>
	</tr>
	<tr>
		<td align="left" style="border: 1px solid black;">write-log</td>
		<td align="left" style="border: 1px solid black;">The primary function. All other functions are called by this function.
			<br>
			<br>
			<strong>Parameters:</strong>
			<br>
			<br>
			<strong style="text-decoration: underline;">logMessage</strong> (required) - Log message.
			<br>
			<strong style="text-decoration: underline;">logTime</strong> (optional) - If not specified, calls <code>get-logtime</code> function.
			<br>
			<strong style="text-decoration: underline;">logType</strong> (optional) - Type of log message (error, trace, debug, etc). If not specified, defaults to <code>0</code> (Error). Dependent on <code>$global:logLevel</code> (see below).
			<br>
			<strong style="text-decoration: underline;">messageContext</strong> (optional) - If not specified, calls <code>get-function -context 2</code> to determine calling function. Can be specified in order to identify other event contexts in log.
			<br>
			<strong style="text-decoration: underline;">logWho</strong> - Uses global variable <code>$global:executingUser</code>. Preferred to determine user with <code>Security.Principal.WindowsIdentity]::GetCurrent().Name</code>.
			<br>
			<strong style="text-decoration: underline;">path</strong> (optional) - If not specified, uses global variable <code>$global:logPath</code>.
			<br>
			<strong style="text-decoration: underline;">file</strong> (optional) - If not specified, uses global variable <code>$global:logFile</code>.
			<br>
			<strong style="text-decoration: underline;">silent</strong> (optional) - If not specified, uses global variable <code>$global:silentOperation</code>.
			<br>
			<br>
			The function also relies on another global variable, <code>$global:logLevel</code>, which determines the default behavior of various logs. If a message's $logType is less than $global:logLevel, it will be written to log, and if $global:silentOperation is $false, written to the screen buffer. This allows for script debugging and verbose logging by modifying the value of a single variable (<code>$global:logLevel</code>).</td>
	</tr>
</table>


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
   `2017-05-14 14:55:17.815 bwhitehead0 - Logging a message.`

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

**More advanced logging**

```Powershell
$global:executingUser=[Security.Principal.WindowsIdentity]::GetCurrent().Name
$global:logPath="D:\log"
$global:logFile="myLog.txt"
$global:silentOperation=$false # Write to screen
$global:logLevel=3 # Debug
$global:dbLogfile="DB.log"

function insert-record
  {
    # Dummy, functionally-incomplete example of DB insert.
    $connection = New-Object System.Data.SqlClient.SqlConnection("Data Source=$databaseServer; Initial Catalog=$databaseName; Integrated Security=SSPI")

    try {
        write-log -logMessage "Opening database" -logType 5
        write-log -logMessage "Connecting to database server $databaseServer, database $databaseName" -logType 2 -file $global:dbLogFile 
        $connection.Open()
        write-log -logMessage "Successfully connected to database" -logType 2 -file $global:dbLogFile 
    }
    catch {
        write-log -logMessage "Error connecting to database: $($_.exception.message)" -file $global:dbLogFile 
        write-log -logMessage "Error connecting to database: $($_.exception.message)"
    }

    $sqlCmd=$connection.CreateCommand()
    $sqlQuery="INSERT testtable VALUES ('name','other value','whatever')"
    write-log -logMessage "Building SQL query: $sqlQuery" -logType 2 -file $global:dbLogFile
    $sqlCmd.CommandText=$sqlQuery
    <#
    code cut out
    #>

    $connection.Close()
}
```
For each section of code, the process would be:
* Log "Opening database" to the global logfile with logType `5` to override `$global:logLevel` settings and ensure it's written to the log.
* Log "Connecting to database server [server], database [DB]" to the database-specific logfile, specifying logType `2` (info).
* Log "Successfully connected to database" to the database-specific logfile, specifying logType `2` (info).  
&nbsp;*If error in the above, instead:*
* Log "Error connecting to database: [Error message]" to the database-specific logfile.
* Log "Error connecting to database: [Error message]" to the global logfile.  
&nbsp;*Continuing:*
* Log "Building SQL Query: [Query text]" to the database-specific logfile, specifying logType `2` (info).

This enables one to use multiple logs for multiple purposes.


# Other Notes

The primary function `write-log` has comment-based help built in, and includes some examples.

In the future, it's possible that the following changes will be made:
1. Remove `-logTime` as a parameter, always calling function `get-logtime` from function `write-log`.
2. Change default value for `$global:logLevel` to a value of either 1 or 2 in order to make it easier to include a base level of verbosity for non-error events.
