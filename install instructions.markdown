---
title: "Manual installation on a Raspberry Pi"
description: "Instructions to install Home Assistant on a Raspberry Pi running Raspbian Lite."
redirect_from: /getting-started/installation-raspberry-pi/
---
Install [Raspberry Raspbian](https://www.raspberrypi.org/downloads/) with desktop either for the raspberry pi or desktop PC depending on your hardware.
Initial setup of raspberry Debian is easier with monitor keyboard and mouse hooked up local to your device.
Enable SSH in configuration, once you have enabled SSH then remote configuring is preferred for below instructions. 
You can copy and paste below commands into the terminal, these instructions assume you have knowledge on the ability to SSH into your device.
Copy and paste is the easiest, and [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) allows right click paste.
These steps use the default install instructions of home assistant and the default user of pi, so if using different then what is default make changes as needed.

(Note some stuff will not work fully until all the steps have been done IE shared folders won't connect until that directory is made.)  

This instruction will do the following:

1. Will allow network shares of certain folders for editing and [u]**backing up**[/u] your configuration of Home assistant files and access to root directory.
 **((WARNING MESSING AROUND IN ROOT DIR CAN BREAK YOUR INSTALL))**

2. Will allow Home assistant to restart after a reboot.

3. Will boot into Kiosk mode for using Lovelace in touchscreen on the same device as home assistant. 
(does not effect headless operation so can use the same instructions)

**"DO NOT COPY AND PASTE STUFF IN QUOTES"**

"LOG INTO THE TERMINAL."

</div>


```
sudo apt update

sudo apt upgrade -y

sudo apt autoremove -y

```

"STEPS BELOW ALLOWS SHARING OF FOLDERS OVER YOUR LOCAL NETWORK."

```bash

sudo apt install samba -y

```
```

sudo nano /etc/samba/smb.conf

```
"CLEAR SCREEN BY HOLDING DOWN THE CTRL AND K KEYS."

"COPY ALL BELOW THIS LINE DOWN TO NOT INCLUDING END."

```

[global]
netbios name = HomeAssistant
server string = The HA File Center
workgroup = WORKGROUP
hosts allow =
socket options = TCP_NODELAY IPTOS_LOWDELAY SO_RCVBUF=65536 SO_SNDBUF=65536
remote announce =
remote browse sync =

[HOME PI]
path = /home/pi
comment = No comment
browsable = yes
read only = no
valid users =
writable = yes
guest ok = yes
public = yes
create mask = 0777
directory mask = 0777
force user = root
force create mode = 0777
force directory mode = 0777
hosts allow =

[HOME ASSISTANT]
path = /home/homeassistant/.homeassistant/
comment = No comment
browsable = yes
read only = no
valid users =
writable = yes
guest ok = yes
public = yes
create mask = 0777
directory mask = 0777
force user = root
force create mode = 0777
force directory mode = 0777
hosts allow =

[ROOT]
path = /
comment = No comment
browsable = yes
read only = no
valid users =
writable = yes
guest ok = yes
public = yes
create mask = 0777
directory mask = 0777
force user = root
force create mode = 0777
force directory mode = 0777
hosts allow =
```

"END"

"PRESS CRTL+O (write out), ENTER, CTRL+X (exit)."

```
sudo smbpasswd -a pi

```
"ENTER THE PASSWORD IT WILL ASK TWICE."
```
sudo service smbd restart

```
"STEPS BELOW MAKES THE RASPBERRY PI BOOT INTO KIOSK SKIP TO NEXT IF NOT USING."
```
sudo apt install sshpass

```
```
cd .config

mkdir lxsession

cd lxsession

mkdir LXDE-pi

sudo nano ~/.config/lxsession/LXDE-pi/autostart

```
"COPY ALL BELOW THIS LINE DOWN TO NOT INCLUDING END."

```

lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
#@xscreensaver -no-splash
@point-rpi
@xset s off
@xset -dpms
@xset s noblank
@sed -i 's/"exited_cleanly": false/"exited_cleanly": true/' ~/.config/chromium-browser/Default/Preferences
@chromium-browser --noerrdialogs --disable-infobars --kiosk http://localhost:8123
```

"END"

"PRESS CRTL+O (write out), ENTER, CTRL+X (exit)."
```
cd

sudo apt-get install bluetooth bluez blueman

```
"STEPS BELOW MAKES HOME ASSISTANT START ON REBOOT."
```
sudo nano -w /etc/systemd/system/home-assistant@homeassistant.service

```
"COPY ALL BELOW THIS LINE DOWN TO NOT INCLUDING END."
```
[Unit]
Description=Home Assistant
After=network-online.target

[Service]
Type=simple
ExecStartPre=/bin/sleep 10
User=%i
ExecStart=/srv/homeassistant/bin/hass -c "/home/homeassistant/.homeassistant"

[Install]
WantedBy=multi-user.target
```

"END"

"PRESS CRTL+O (write out), ENTER, CTRL+X (exit)."
```
sudo systemctl --system daemon-reload

sudo systemctl enable home-assistant@homeassistant

```
```
sudo apt install xdotool unclutter sed

```
"STEPS BELOW INSTALLS HOME ASSISTANT."
```
sudo apt install python3 python3-dev python3-venv python3-pip libffi-dev libssl-dev -y

```
```
sudo apt install libbluetooth-dev

```
```
sudo apt install python-dev

sudo pip install PyBluez

```
```
sudo useradd -rm homeassistant -G dialout,gpio,i2c

cd /srv
sudo mkdir homeassistant
sudo chown homeassistant:homeassistant homeassistant

sudo -u homeassistant -H -s
cd /srv/homeassistant
python3 -m venv .
source bin/activate

python3 -m pip install wheel

pip3 install homeassistant

pip3 install home-assistant-frontend

hass

```
</div>
"GIVE IT ABOUT 10 MINS AND REBOOT THE SYSTEM, IF YOU HAVE A MONITOR CONNECTED TO THE RASPBERRY IT SHOULD DISPLAY THE HOME ASSISTANT PAGE."
