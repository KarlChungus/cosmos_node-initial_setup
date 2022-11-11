# Inital Setup Guide for Cosmos Nodes
Written form the perspective of using Ubuntu with a dedicated server or VPS, but most of this will apply if you are running a bare metal machine as well. If running on RHEL/CentOS, Arch, or FreeBSD you'll likely need to search for comaptible packages in both official and community repos. Some commands may be slightly different as well.  

For dedicated server or VPS providers please look into `Hetzner` `Digital Ocean` `Contabo` and others. If you want to experiment with a decentralized solution, consider [Akash](https://github.com/ovrclk)

Whenever you see `gaia` used make sure to replace with whatever node you are installing.  
  
## LOGIN
`ssh root@ipaddress`

## Update & Upgrade Repos
`apt update && apt upgrade -y`

## Add Users  
Replace `gaia` with an alternate name if installing a different node.
  
`adduser username`  
`adduser gaia`  

## Give User Sudo
Create at least two user accounts: one for yourself and one for installing your node  
  
`usermod username -aG sudo`  
`usermod gaia -aG sudo`

## Switch to Node user
`su - gaia`

## Install Standard Dependencies
`sudo apt install make build-essential gcc git jq chrony -y`

## Install Go  
Check https://go.dev/dl for latest version before running  
  
`wget https://golang.org/dl/go1.18.4.linux-amd64.tar.gz`  
`sudo tar -C /usr/local -xzf go1.18.4.linux-amd64.tar.gz`

## Setup go in .profile  
Once Go is downloaded and extracted to `/usr/local/` you can add this to any user's `.profile`  
  
`cat <<EOF >> ~/.profile`  
`# Go`  
`GOROOT=/usr/local/go`  
`GOPATH=$HOME/go`  
`GO111MODULE=on`  
`PATH=$PATH:/usr/local/go/bin:$HOME/go/bin`  
`EOF`

## Reset shell session
`source ~/.profile`

## Look for go to confirm its installation
`go version`  
`which go`

## SSH Configuration
`sudo nano /etc/ssh/sshd_config`
  
Uncomment `Port 22` and change `22` to `xxxx` (`xxxx` = custom port)  
  
Add or uncomment the line `AllowUsers username1 username2` to restrict ssh login to defined users only. Edit `username1` with the user you wish to grant ssh access to. This will prevent ssh logins using your `gaia` or node user account.
  
Press CTRL+X and save your file  
  
`sudo systemctl restart sshd`

## UFW Rules  
This is just an example using default ports, please set rules according to your setup and specific needs:  
  
`sudo ufw default allow outgoing`  
`sudo ufw default deny incoming`  
`sudo ufw allow xxxx`  
`sudo ufw allow 26656`  
`sudo ufw allow 9090`

## Open RPC (default: 26657) 
Open RPC to your specific local machine's IP address to connect and interact with your node remotely. This rule will need to be upated whenever your local IP changes. 
  
`sudo ufw allow from [youripaddress] to any port 26657`  
`sudo ufw enable`

## Confirm with UFW that your custom ssh port is open
`sudo ufw status`

## Open a second terminal session and try to login using your user's creditinals.
`ssh -p xxxx user@ipaddress`

## Test sudo and root access
`sudo -i`  
`exit`

## Restrict Root Login
`sudo nano /etc/ssh/sshd_config`  
  
 Change 'PermitRootLogin' from "Yes" to "No" and confirm it is uncommented.  
   
`CTRL+X and Save`  
`sudo systemctl restart sshd`

## Exit Root & User
`exit`

## Verify Root Login is Disabled
`ssh -p xxxx root@ipaddress`  

## Verify Node User Login is Disabled  
`ssh -p xxxx gaia@ipaddress`  

You are now ready to [install a node](https://github.com/reversesigh/cosmos_node-initial_setup/blob/main/02_install_node.md)  
