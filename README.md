# Concept

* RPi in remote location
* USB drive attached to RPi
* RPi storage:
    *  **main partition**: unencrypted, used to open `sshd`
    *  **second partition** or **usb stick**: encrypted, tiny; stores credentials for Wireguard and restic; prevents loss of data in case of device loss
* Register external IP address with some DynDNS service, forward SSH to Pi; disable password login

# Process

* start RPi
* connect via SSH, decrypt second partition/usb stick
* connect back via decrypted Wireguard config/credentials
* mount smb drives via decrypted credential files
* perform backup via restic

# Hardware

get a raspberry pi, install Raspbian

# tools

## LUKS

```
sudo apt install cryptsetup
```

## wireguard

`sudo apt install wireguard`

## restic

see also https://github.com/Momro/restic-backup

Change RESTIC_REPOSITORY to backup disk, see below

# encrypt partition/stick

```
# mount partition/drive to /dev/mapper/secretsDrive
sudo cryptsetup luksOpen /dev/sdc1 secretsDrive

# format with ext4
sudo mkfs.ext4 /dev/mapper/secretsDrive

# mount secretsdrive to /media/cryptostorage
sudo mount /dev/mapper/secretsDrive /media/cryptostorage
```

# mount

## original/remote folder

```
$ sudo mount -t cifs //<ip address>/<remote share> /mnt/original/<remote share> \
  -o credentials=/root/smbcred/original/<remote share>,uid=1000,gid=1000,\
file_mode=0775,dir_mode=0775,noperm,forceuid,forcegid,vers=3.0

# cat /root/smbcred/original/<remote share>
username=<share username>
password=<share password>
```

### auto mount

set up cron job to mount, as described in 

```
$ sudo su # MUST BE ROOT!
$ crontab -e


# mount <remote share>
@reboot sleep 60 && mount -t cifs //<ip address>/<remote share> /mnt/original/<remote share> -o credentials=/root/smbcred/original/<remote share>,uid=1000,gid=1000,file_mode=0775,dir_mode=0775,noperm,forceuid,forcegid,vers=3.0

# periodically perform backup, and forget old backups
0 3 * * * /root/backup/backup.sh do-backup && /root/backup/backup.sh do-forget
```

## backup disk

```
mkdir /media/backup-usb-drive

lsblk

# if NTFS
sudo apt install ntfs-3g
sudo mount -t ntfs-3g -o uid=1000,gid=1000 /dev/sda1 /media/backup-usb-drive

# if need to format, go ext4:
sudo mkfs.ext4 -L usbhd /dev/sda1
sudo mount -t ext4 /dev/sda1 /media/backup-usb-drive
```

# test backup

```
./backup.sh do-backup
ls -R /media/backup-usb-drive
```

# encrypt RaspPi SD card

* how do we protect the content of the SD card?
* how do we protect the Wireguard access config?
* do we need to turn off HDMI?

# report proper backup

how?
