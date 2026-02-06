# Hardware

get a raspberry pi, install Raspbian

# tools

## wireguard

`sudo apt install wireguard`

## restic

see also https://github.com/Momro/restic-backup

# mount

## original/remote folder

```
$ sudo mount -t cifs //<ip address>/<remote share> /mnt/original/<remote share> \
  -o credentials=/root/.smbcred-original-<remote share>,uid=1000,gid=1000,\
file_mode=0775,dir_mode=0775,noperm,forceuid,forcegid,vers=3.0

# cat /root/smbcred/original/<remote share>
username=<share username>
password=<share password>
```

## backup disk

tbd.

# test backup

tbd.

# encrypt RaspPi SD card

* how do we protect the content of the SD card?
* how do we protect the Wireguard access config?
* do we need to turn off HDMI?

# report proper backup

how?
