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






# Another table attempt

<table cellspacing="0" border="1">
	<colgroup width="200"></colgroup>
	<colgroup width="800"></colgroup>
	<tr>
		<td width="200"><strong>Function Name</strong></td>
		<td align="center"><strong>Purpose</strong></td>
	</tr>
	<tr>
		<td align="left">write-data</td>
		<td align="left">Handles the writing of log data to disk using .NET <code>System.IO.File</code>.
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
		<td align="left">get-logtime</td>
		<td align="left">Returns current date in as type string, in format <pre>yyyy-MM-dd HH:mm:ss.fff</pre></td>
	</tr>
	<tr>
		<td align="left">get-function</td>
		<td align="left">Returns the name of the function writing the log.
			<br>
			<br>
			<strong>Parameters:</strong>
			<br>
			<br>
			<strong style="text-decoration: underline;">context</strong> (optional) - If not specified, defaults to <code>1</code>. This is used to determine what function is making the log request. Context of <code>1</code> will identify the function that called get-function. Context of <code>2</code> will identify the function that called the function that called <code>get-function</code>. If not specified, the function <code>write-log</code> will use <code>-context 2</code>.</td>
	</tr>
	<tr>
		<td align="left">write-log</td>
		<td align="left">The primary function. All other functions are called by this function.
			<br>
			<br>
			<strong>Parameters:</strong>
			<br>
			<br>
			<strong style="text-decoration: underline;">logMessage</strong> (required) - Log message.
			<br>
			<strong style="text-decoration: underline;">logTime</strong> (optional) - If not specified, calls <code>get-logtime</code> function.
			<br>
			<strong style="text-decoration: underline;">logType</strong> (optional) - Type of log message (error, trace, debug, etc). If not specified, defaults to <code>0</code> (Error).
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











