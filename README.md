# InvokeAsAdmin
A novice PowerShell module that provides cmdlet and process elevation without creating a new console window.

Available on [PSGallery](https://www.powershellgallery.com/packages/InvokeAsAdmin). Try it out with:
```powershell
Install-Module -Name InvokeAsAdmin -Repository PSGallery -Scope CurrentUser
```

## Description

So here's how it works: In order to run an administrative command and get the contents on the same command-line in Windows NT, some clever trickery needs to happen. Because of the ways that user and process management are currently implemented, you cannot start a process with only the security privileges of another user; you have to explicitly execute the process as the other user.

While it is novel to start a process as another user, especially with the powershell.exe -runas flag, it is another level of conventional difficulty to return the command output to the original console instance.

This is where kernel IPC comes into play: by setting up a named pipe in the admin process, output can be returned through said pipe to the user process.

The Invoke-AsAdmin cmdlet executes the command specified by the arguments as an elavated user.

When the command is a single string, it is executed by the Invoke-Expression cmdlet. Otherwise, the ampersand (&) shell command is used.

The command line is then executed in an elavated process that is different from the caller. The output is serialized and transfered as a pipeline stream to the caller. If the output is not serializable, it is converted to a text stream by means of the Out-String cmdlet.

The Invoke-AsAdmin cmdlet will not open a new console window. Instead, it executes the command utilizing the same console session as the caller process. All environment variables are evaluated in the context of the caller process.

### Example 1

```powershell
Invoke-AsAdmin {Get-Process -IncludeUserName | Sort-Object UserName | Select-Object UserName, ProcessName}
```
This will obtain a process list with user name information, sorted by UserName. Because the System.Diagnostics.Process objects are not serializable, if you want to transform the output of Get-Process, enclose the command with curly braces to ensure that pipeline processing should be done in the called process.

### Example 2

```powershell
Invoke-AsAdmin {cmd /c mklink $env:USERPROFILE\bin\test.exe test.exe}
```

This will reate a symbolic link to test.exe in the $env:USERPROFILE\bin folder. Note that $env:USERPROFILE is evaluated in the context of the caller process.

### Thanks for looking!

I'm just a novice user who codes for fun. This was originally put together by a user named msumimz, however I have been unable to find them online and the original posts for this code have disappeared as far as I have been able to search. I saved this almost four years ago and have hacked a bit with it ever since. Here it is with with the author's credit and it's original license. I'll do what I can to continue working on this.
