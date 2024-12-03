# Setting up Ubuntu from scratch

## Starting with GNOME

* Change appearance settings to suit (much easier now - don't need to use GNOME tweaks for dock)
* Install brave [https://brave.com/linux/](https://brave.com/linux/)
* Install vim
* Change CAPS key behaviour
```
sudo apt install gnome-tweaks
gnome-tweaks
```
    * Go to keyboard and mouse settings and under additional layout options, click caps lock behaviour and make it an additional escape key.

* Change keyboard layout to US


## Set up SSH

### Install things
```
sudo apt install openssh-server && sudo apt install openssh-client

sudo systemctl status openssh
```

### Config File
Edit the ssh configuration file:
```
PasswordAuthentication no
PermitRootLogin no
```

### Authorized Keys
Add keys to the authorized keys file:
```
curl https://github.com/{your-username}.keys > ~/.ssh/authorized_keys
```
or
```
curl https://github.com/{your-username}.keys >> ~/.ssh/authorized_keys
```
depending on whether you want to create the file with only those contents or append to what is already there.


### SSH Daemon 
Check that ssh is running and start/enable if not:
```
sudo systemctl status ssh

# if not running
sudo systemctl start ssh
sudo systemctl enable ssh

sudo systemctl status sshd

# if not running
sudo systemctl start sshd
sudo systemctl enable sshd
```
If you need to reload the config, just reload and/or restart the ssh service:
```
sudo systemctl reload ssh
sudo systemctl restart ssh
```


## Neovim
Install npm, and ripgrep:
```
sudo apt install npm ripgrep
```

Add neovim to the ppa:
```
# add the unstable ppa because a lot of plugins won't work on v0.7.2
sudo add-apt-repository ppa:neovim-ppa/unstable
sudo apt-get update
sudo apt-get install neovim
```

* Copy your nvim config into ~/.config/nvim
* Open nvim and go to `lua/{name}/packer.lua`
* `:so`, `:PackerSync`, `PackerUpdate`


## Tmux
```
sudo apt install tmux
```

To get the colours in tmux:
```
set -g default-terminal "tmux-256color"
set-option -ga terminal-overrides ',xterm-256color:Tc'
```
add this to your ~/.tmux.conf and then reload tmux (or open it for the first time).


## Miniconda
```
mkdir software
cd software
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```
and then go through the instructions. Miniconda is then installed.
