# Reverse SSH tunneling
This section allows you to set up a secure server, such as an
EC2 instance.

This example will show you how to do so with an EC2 instance.

## Setting up EC2
First you want to spin up your EC2 instance. After doing this,
you then want to add a security group rule: custom TCP rule on
port XXXX, with the source coming from your IP range for
maximum security.

Also make sure you have downloaded your key to access the VM.

## Setting up your machine

### SSH
Your machine will want to have the ssh server set up. This can
be installed by running
```
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install openssh-server
```

You may then want to configure the settings for your ssh server
in `/etc/ssh/sshd_config` - this could involve allowing password
access (heavily discouraged but may be useful for testing). 
After doing this, you run
```
sudo systemctl restart ssh
```
to restart the ssh daemon and load your modified config.

### Firewall
To set up the firewall, you will need to allow ssh connections
on the default port (22):
```
sudo ufw allow 22
```

then you would either like to reload the firewall, or start it
if it isn't already running.

Check if running:
```
sudo ufw status
```
Start firewall:
```
sudo ufw enable
```
Reload firewall:
```
sudo ufw reload
```

### Your machine is all done!
Until everything is sorted on the VM, this is all you need to do
here for now.


## Setting up the VM

### Firewall
You should probably sort the firewall here too. Using the same
commands to check whether the firewall is up or not, you need
to make sure that the ssh port is open to stop yourself being
locked out. After you have added port 22, you also need to
allow connections through port XXXX.

This can be done using:
```
sudo ufw allow XXXX/tcp
```

Once this is done, you want to reload or start (if your firewall
isn't already up) ufw.

### SSH settings
To allow connections through your VM without having to ssh into
your VM and then ssh into your machine from there, you need
to enable `GatewayPorts`. This can be done by editting the
`sshd_config` file at `/etc/ssh/sshd_config`. This file needs
to be editted with `sudo`. All that needs to be done is either
uncomment the line `GatewayPorts yes`, or add that line to the
bottom of the file.


## Setting up the reverse tunnel
Once all the configuration is set up, it is time to run the
reverse tunnel on your machine. Later we will look at how
you can set this up as a background process on startup to
be able to ssh into your machine whenever it is turned on.

Set up the reverse tunnel with:
```
ssh -i path/to/ssh.pem -R 0.0.0.0:XXXX:localhost:22 vm-username@your.vm.ip.address
```

If you notice, the 0.0.0.0 binds to all interfaces on the VM. The access
control is all handled by the EC2 instance, either through the
security groups or through the VM's firewall.

## SSHing into your machine
After all this is set up, you can ssh into your machine using:
```
ssh -i path/to/ssh.pem -p XXXX machine-username@your.vm.ip.address
```

Now you're all done and can access your terminal from wherever you
might be!
