# Life in testing

Living in Debian testing is awesome, but has its issues as well...

## Fixing Gnome keyring so that it doesn't provide ssh-agent functionality

When using my RSA key to ssh a remote machine, I got an error:

```
Agent admitted failure to sign using the key.
```

After looking around, I found a [very good description of my problem for Ubuntu](https://chrisjean.com/ubuntu-ssh-fix-for-agent-admitted-failure-to-sign-using-the-key/). Unfortunately, the answer provided there didn't work for me, because it involves using a graphical user interface to disable Gnome services on startup, which seems to be available only in Unity. The description includes a test to be sure what the problem is, and a good description of what is happening. The problem is related to gnome-keyring providing ssh-agent services, but not handling well RSA keys.

Fortunately, I fond a [page describing how to disable ssh-agent service for gnome-keyring](http://blog.josefsson.org/tag/keyring/). The fix is simple:

```
jas@latte:~$ mkdir ~/.config/autostart
jas@latte:~$ cp /etc/xdg/autostart/gnome-keyring-ssh.desktop ~/.config/autostart/
jas@latte:~$ echo 'Hidden=true' >> ~/.config/autostart/gnome-keyring-ssh.desktop 
```

And after that, logout and login again. And done! The only trouble is that now I have to manually use ssh-add for ssh-agent to include the new keys, which are no longer stored in the Gnome keyring.

## Stopping automatic download of software updates

2015-08-08

It seems my laptop is downloading software updates periodically. This is handy, because when I want to update the software packages, they are already there. But it is a problem when I'm connected through low bancwidth networks. So I decide to disable that. The main problem was to know whcih application was doing the updates. After considering some candidates, including packagekit and some of its related packages, it seems it is gnome-software. To disable in it, you have to tinker a bit with dconf-editor. No rocket science, just set to False (unckeck, in the graphical interface) the property "download-updates" in org.gnome.software.

## Installing Skype (and enabling multiarch)

The Skype I download from skype.com seems to be for i386 (32 arch, not 64 arch, which is the arch I have in my laptop). I follow instructions in the [Debian Wiki for Skype](https://wiki.debian.org/skype). I enable multiarch, and then install the regular Skype for Linux (the 5th step is for fixing missing dependencies, if any, the 6th is for finishing skype configuration, but I'm not sure it's needed).

```
dpkg --add-architecture i386
apt-get update
wget -O skype-install.deb http://www.skype.com/go/getskype-linux-deb
dpkg -i skype-install.deb
apt-get -f install
dpkg -i skype-install.deb
```
## Installing add-apt-repository

This is a convenient tool to manage apt repositories. In fact, I installed the gtk version, which intalls as a dependency the command line tool:

```
apt-get install software-properties-gtk
```

That's it...
