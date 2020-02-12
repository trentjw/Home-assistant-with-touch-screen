### This instruction Sets up the connected Touch Screen Monitor to respond to the Command Line Switch.
**Enables turn on or Turn off from Home Assistant thus allowing automation of it,**

"For example turn on when you get home by sensor or presence"

This instruction assumes that you already have your touch screen connected and working on your Home assistant Raspberry PI.

This instruction assumes that you are using PI as your default user name in Rasperry, if not edit as needed.

</div>

1. **SSH into your Home assistant computer as usual.**

2. **Update your system.**

```bash
sudo apt update

sudo apt upgrade -y


```
2. **Clean up your System.**
```bash
sudo apt autoremove -y

```

3. **Install sshpass.**
```bash
sudo apt install sshpass

```
4. **Change current user to your home assistant user name.**
  - "default is homeassistant" if default copy and paste 
  ```bash  
  sudo -u homeassistant -H -s
  
  ssh pi@127.0.0.1 'export DISPLAY=:0 && xset dpms force on'
  
  ```  
5. **It will ask if you want to permanently add it.**  
 - Type yes.
 - enter **"your password"** for PI

6. **below command lines should now work for you in your Home assistant switch configuration page.**
   </div>   
```
>  - platform: command_line
    switches:
      touch_screen:
        command_on:  sshpass -p YOUR_PASSWORD ssh pi@127.0.0.1 'export DISPLAY=:0 && xset dpms force on'
        command_off:  sshpass -p YOUR_PASSWORD ssh pi@127.0.0.1 'export DISPLAY=:0 && xset dpms force off'
        command_state:  sshpass -p YOUR_PASSWORD ssh pi@127.0.0.1 'export DISPLAY=:0 && xset -q | grep "Monitor is" | cut -c14-17' | grep "On" > /dev/null
        
```        
  
      
