# Preview
 
## Base:
___
 
### BTRFS subvolume for home (Timeshift)
```shell
btrfs subvolume list /
btrfs subvolume create /@home
mv /home/* /@home
mount -o subvolid=5 /dev/... /mnt
mv '/mnt/@rootfs/@home' /mnt
mv '/mnt/@rootfs' '/mnt/@'
umount /mnt
# Update fstab to reflect change... duplicate rootfs and change to @ and @home
vi /etc/fstab
dpkg-reconfigure grub-pc
reboot
# dpkg-reconfigure grub-efi-amd64
```

### Configuration
```shell
usermod -g users tracnac
chmod -R tracnac:users /home/tracnac
sed -i 's/^# fr_FR.UTF-8 UTF-8/fr_FR.UTF-8 UTF-8/' /etc/locale.gen
locale-gen
```

### Mail
```shell
apt install bsd-mailx msmtp exim4-base- ssmtp- sendmail-base- opensmtpd- nullmailer-
cat > /etc/msmtprc << EOF
account default
auth on
host smtp.mailfence.com
from tracnac@devmobs.fr
user ********
password ********
tls on
tls_starttls off
tls_trust_file /etc/ssl/certs/ca-certificates.crt
aliases /etc/aliases
syslog LOG_MAIL
EOF
cat > /etc/aliases << EOF
default: tracnac@devmobs.fr
EOF
chown root:mail /etc/msmtprc /etc/aliases
chmod 0640 /etc/msmtprc /etc/aliases
```

### Sysadmin tools
```shell
apt install software-properties-common
apt-add-repository non-free
apt-add-repository contrib
apt update
apt install bash-completion git vim tmux build-essential ca-certificates curl wget gnupg lsb-release uuid-runtime cmake apt-transport-https pkg-config pinentry-gnome3 pass pass-git-helper git-crypt
```
 
### Firmware    
```shell
apt install amd64-microcode firmware-iwlwifi firmware-realtek firmware-misc-nonfree
```
 
### Daemon    
```shell
apt install acpid
```
 
### Gnome minimal install
```shell
apt install gnome-core chrome-gnome-shell gnome-weather aspell-fr xsel xclip gnome-tweaks cups cups-filters
```

### Setup for Gnome
```shell
sed -i 's/^managed=false/managed=true/' /etc/NetworkManager/NetworkManager.conf
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
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/signal-desktop-keyring.gpg] https://updates.signal.org/desktop/apt xenial main' |  tee -a /etc/apt/sources.list.d/signal-xenial.list
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

# Dummy package for firefox
apt install equivs libdbus-glib-1-2
cat > firefox.equivs <<EOF 
Package: firefox-esr
Version: 99:99
Maintainer: tracnac@devmobs.fr
Architecture: all
Description: dummy firefox-esr package
EOF
equivs-build firefox.equivs
apt install ./firefox-esr_99_all.deb
rm -r /etc/firefox-esr
ln -sf /opt/firefox/firefox /etc/alternatives/x-www-browser
ln -sf /opt/firefox/firefox /etc/alternatives/gnome-www-browser
rm /etc/alternatives/gnome-www-browser.1.gz
rm /etc/alternatives/x-www-browser.1.gz
apt remove equivs
```

### Thunderbird
```shell
apt install libbotan-2-17 libtspi1 libdbus-glib-1-2
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
Icon=/opt/thunderbird/chrome/icons/default/default128.png
Categories=Network;Email;News;GTK;
MimeType=message/rfc822;x-scheme-handler/mailto;text/calendar;text/x-vcard;
StartupWMClass=thunderbird
StartupNotify=true
Keywords=EMail;E-mail;Contact;Addressbook;News;
EOF
```

### Lang
```shell
apt install valac golang clang clang-format clang-tools strace autoconf automake rr lldb 
```
 
### Music & Video
```shell
apt install mpd mpc mpv
systemctl disable --now mpd

mkdir /opt/Music/{Albums,Playlists}
chown root:audio /opt/Music/{Albums,Playlists}
chmod 0775 /opt/Music/{Albums,Playlists}

mkdir /home/tracnac/.config/mpd
cat > /home/tracnac/.config/mpd/mpd.conf << EOF
music_directory		"/opt/Music/Albums"
playlist_directory	"/opt/Music/Playlists"

db_file			"/home/tracnac/.config/mpd/tag_cache"
log_file		"/home/tracnac/.config/mpd/mpd.log"
pid_file		"/home/tracnac/.config/mpd/pid"
state_file		"/home/tracnac/.config/mpd/state"
sticker_file    "/home/tracnac/.config/mpd/sticker.sql"
bind_to_address		"localhost"

auto_update         "yes"
input {
        plugin "curl"
}
audio_output {
	type		"pulse"
	name		"MPD output"
}
filesystem_charset	"UTF-8"
EOF

cat > /opt/Music/Playlists/radioparadise.m3u <<EOF
#EXTM3U
#EXTINF:0,Radio Paradise
https://stream.radioparadise.com/aac-320
EOF

# As user
# systemctl --user enable mpd
# systemctl --user start mpd
```

TODO:
- Fonts
- Rework
- Sudo
- Create shell scripts