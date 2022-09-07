# Debian 11.4
 
## Base:
___
 
### Sysadmin tools
```shell
apt install software-properties-common
apt-add-repository non-free
apt-add-repository contrib
apt update
apt install bash-completion git vim tmux build-essential ca-certificates curl wget gnupg lsb-release uuid-runtime cmake apt-transport-https pkg-config pinentry pass pass-git-helper git-crypt
```
 
### Firmware    
```shell
apt install amd64-microcode firmware-iwlwifi firmware-realtek firmware-misc-nonfree
```
 
### Daemon    
```shell
apt install acpid cups cups-filters
```
 
### Gnome minimal install
```shell
apt install gnome-core chrome-gnome-shell gnome-weather aspell-fr xsel xclip gnome-tweaks
```

### Setup for Gnome
```shell
sed -i 's/^managed=false/managed=true/' /etc/NetworkManager/NetworkManager.conf
sed -i 's/^# fr_FR.UTF-8 UTF-8/fr_FR.UTF-8 UTF-8/' /etc/locale.gen
locale-gen
```

### Nvidia
```shell
apt install linux-headers-$(uname -r)  dkms libglvnd-dev
https://us.download.nvidia.com/XFree86/Linux-x86_64/515.65.01/NVIDIA-Linux-x86_64-515.65.01.run
bash NVIDIA-Linux-x86_64-515.65.01.run
```
 
### Divers
```shell
chgrp users /opt
chmod 0775 /opt
usermod -g users tracnac
```
 
## Userland
___
 
### Docker
```shell
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin 
TODO : docker-desktop
```
 
### VSCode
```shell
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor -o /usr/share/keyrings/microsoft-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft-archive-keyring.gpg] https://packages.microsoft.com/repos/vscode stable main"  | tee /etc/apt/sources.list.d/vscode.list > /dev/null
apt-get update
apt install code
```

### Virtualbox
[Get extension pack](https://www.virtualbox.org/wiki/Downloads)
```shell
wget -O- https://www.virtualbox.org/download/oracle_vbox_2016.asc | gpg --dearmor -o /usr/share/keyrings/oracle-virtualbox-2016.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] https://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib"   | tee /etc/apt/sources.list.d/virtualbox.list > /dev/null
apt-get update
apt install virtualbox-6.1
```

### Signal
```shell
wget -O- https://updates.signal.org/desktop/apt/keys.asc | gpg --dearmor -o /usr/share/keyrings/signal-desktop-keyring.gpg
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/signal-desktop-keyring.gpg] https://updates.signal.org/desktop/apt xenial main' |  sudo tee -a /etc/apt/sources.list.d/signal-xenial.list
apt update
apt install signal-desktop
```

### GnomeBuilder & Glade
```shell
apt install gnome-builder glade libgtk-3-dev appstream-util
```
 
### Android studio
[Get Android Studio](https://developer.android.com/studio)
```shell
apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
wget -O- 'https://redirector.gvt1.com/edgedl/android/studio/ide-zips/2021.2.1.16/android-studio-2021.2.1.16-linux.tar.gz' | tar xvz -C /opt
```
 
### Firefox
```shell
apt remove firefox
apt autoremove
wget -O- --content-disposition 'https://download.mozilla.org/?product=firefox-latest-ssl&os=linux64&lang=en-US' | tar xjv -C /opt
cat > /usr/share/applications/firefox-latest.desktop <<EOF
[Desktop Entry]
Name=Firefox
Comment=Web Browser
Exec=/opt/firefox/firefox %u
Terminal=false
Type=Application
Icon=/opt/firefox/browser/chrome/icons/default/default128.png
Categories=Network;WebBrowser;
MimeType=text/html;text/xml;application/xhtml+xml;application/xml;application/vnd.mozilla.xul+xml;application/rss+xml;application/rdf+xml;image/gif;image/jpeg;image/png;x-scheme-handler/http;x-scheme-handler/https;
StartupNotify=true
Actions=Private;

[Desktop Action Private]
Exec=/opt/firefox/firefox --private-window %u
Name=Open in private mode
EOF
cd ~
```

### Thunderbird
```shell
wget -O- --content-disposition 'https://download.mozilla.org/?product=thunderbird-latest-SSL&os=linux64&lang=en-US' | tar xjv -C /opt
cat > /usr/share/applications/thunderbird-latest.desktop <<EOF
[Desktop Entry]
Name=Thunderbird Stable
Comment=Read/Write Mail/News with Thunderbird
GenericName=Mail Client
Exec=/opt/thunderbird/thunderbird %u
Terminal=false
X-MultipleArgs=false
Type=Application
Version=1.0
Icon=/opt/thunderbird/browser/chrome/icons/default/default128.png
Categories=Network;Email;News;GTK;
MimeType=message/rfc822;x-scheme-handler/mailto;text/calendar;text/x-vcard;
StartupWMClass=thunderbird
StartupNotify=true
Keywords=EMail;E-mail;Contact;Addressbook;News;
EOF
cd ~
```

### Lang
```shell
apt install valac golang clang clang-format clang-tools strace autoconf automake rr lldb 
```
 
### Music
```shell
apt install mpd mpc
chown mpd:audio /var/lib/mpd/{music,playlists}
chmod 0775 /var/lib/mpd/{music,playlists}
cat > /var/lib/mpd/playlists/radioparadise.m3u <<EOF
#EXTM3U
#EXTINF:0,Radio Paradise
https://stream.radioparadise.com/aac-320
EOF
```

TODO:
- Audio output for MPD
- Rework
