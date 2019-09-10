---
title: Oneshot Duet Backup
---

The following will create a nice filename timestamped zip, backing up everything on your Duet bar G-codes (for speed) 

### Windows

Create a folder and download the following
1. from  https://www.7-zip.org/download.html - the "7-Zip Extra: standalone console version" and unzip 7za.exe,7za.dll and 7zxa.dll
1. https://github.com/wilriker/rfm/releases - rfm-windows_amd64.zip and unzip into the folder.
1. paste the following code into a file called something like "duetbackup.cmd" in this folder, and modify the third line.

If you are the trusting sort, you can get all of this from here http://www.crimsoncurve.com/upload/duetbackup.zip in one hit.

```
@echo off
REM CHANGE THE VALUE OF THIS TO SUIT YOUR RAILCORE - You can use IP address also
set hostnameorip = railcore.local

REM Sets environment variables used to create unique tape names.
  for /f "Tokens=1-4 Delims=/ " %%i in ('date /t') do set dt=%%k-%%j-%%i_%%l
  for /f "Tokens=1" %%i in ('time /t') do set tm=%%i
  set tm=%tm::=%
  set dtt=%dt%%tm%
REM create temporary folder
mkdir %dtt%

REM use rfm to backup duet, bar gcodes folder to the temp. folder
rfm backup -domain %hostnameorip% -exclude 0:/gcodes .\%dtt% 0:/

REM zip up the contents of the temp folder
7za.exe a  -r %dtt%.zip -w %dtt% 

REM delete the temp folder. Leaving the zip
RD /S /Q %dtt%
```


### Linux

Download rfm from https://github.com/wilriker/rfm/releases for your architecture.

```
DUET=192.168.0.26
FILE=railcore_$DUET_backup             
NAME=${FILE%.*}
DATE=`date +%y-%m-%d`         
NEWFILE=${NAME}_${DATE}
mkdir $NEWFILE
./rfm backup -domain $DUET -exclude 0:/gcodes ./$NEWFILE 0:/
tar -zcvf $NEWFILE.tar.gz $NEWFILE
rm -rf $NEWFILE
```

modify it to your wishes. cron it daily for bonus points
