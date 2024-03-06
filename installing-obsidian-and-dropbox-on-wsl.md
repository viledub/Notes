# Installing Obsidian and Dropbox on WSL

### Installing dropbox&#x20;

```
cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
```

Next, run the Dropbox daemon from the newly created .dropbox-dist folder.

\~/.dropbox-dist/dropboxd

In a separate terminal

```
mkdir ~/scripts && cd ~/scripts wget -O - "https://www.dropbox.com/download?dl=packages/dropbox.py"
```

add the following to bashrc&#x20;

```
alias dropbox='python3 /home/developer/scripts/dropbox.py'
```

Open a new terminal

```
dropbox status
```

Starting... To link this computer to a Dropbox account, visit the following url: ...

Go to the url in a browser and login

Exclude everything from dropbox sync. This will prevent dropbox downloading anything to this machine. New folders created under Dropbox on this machine will be synced however, which is what I want.

```
cd ~/Dropbox 
dropbox exclude add ./*
```

ls should yield an empty dropbox folder

### Installing Obsidian&#x20;

First install flatpak&#x20;

```
sudo apt install flatpack
```

Use flatpack to install obsidian

```
sudo flatpak install flathub md.obsidian.Obsidian
```

The following command will run obsidian

```
flatpak run md.obsidian.Obsidian
```

Add the following to .bashrc&#x20;

```
alias obsidian='flatpak run md.obsidian.Obsidian' 
setxkbmap -layout gb
```

run obsidian with:&#x20;

```
obsidian
```
