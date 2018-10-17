# :ocean: Tightvncserver installation & configure :ocean:

### 1. installation 
- server  
~~~
apt-get install gnome-settings-daemon gnome-terminal metacity nautilus gnome-panel tightvncserver gnome-shell ubuntu-gnome-desktop
~~~
- windows client : https://www.tightvnc.com/download.php

### 2. edit : ~/.vnc/xstartup 
~~~~~~~~
#!/bin/sh
# Uncomment the following two lines for normal desktop:
# unset SESSION_MANAGER
# exec /etc/X11/xinit/xinitrc

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &
x-terminal-emulator -geometry 80x24+16+16 -ls -title "$VNCDESKTOP Desktop" &
x-window-manager &
ubuntu-gnome-desktop &
gnome-shell &
gnome-panel &
gnome-settings-daemon &
metacity &
nautilus &
~~~~~~~~

### 3. operation
- start  
~~~
vncserver  
~~~

- stop  : 
~~~
vncserver -kill :1
~~~
