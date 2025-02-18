To set up an SSH tunneled bastion host for RDP, follow these steps:

1. Configure the bastion host:
    
    - Launch a Linux instance to serve as the bastion host[1](https://smallstep.com/blog/diy-ssh-bastion-host/).
        
    - Disable interactive SSH sessions by updating `/etc/ssh/sshd_config` with the following settings[9](https://goteleport.com/blog/ssh-bastion-host/):
    
		```
		PermitTTY no
		X11Forwarding no
		PermitTunnel no 
		GatewayPorts no 
		ForceCommand /usr/sbin/nologin    
		```
    - Restart the SSH service to apply changes[1](https://smallstep.com/blog/diy-ssh-bastion-host/).
        
- Set up SSH tunneling:
    
    - On your local machine, use the following command to create an SSH tunnel[4](https://paulbradley.org/rdp-ssh-tunnel/):
        
        `ssh -L 3389:WINDOWS_SERVER_IP:3389 BASTION_HOST_IP -l USERNAME -N`
        
    - Replace WINDOWS_SERVER_IP with the private IP of your Windows RDP server, BASTION_HOST_IP with the public IP of your bastion host, and USERNAME with your bastion host username[4](https://paulbradley.org/rdp-ssh-tunnel/).
        
3. Configure RDP client:
    
    - Open your RDP client (e.g., Microsoft Remote Desktop)[2](https://gist.github.com/mcenirm/cbf0f6570d8022328b8057be2791008e).
        
    - Set the connection address to "localhost:3389"[4](https://paulbradley.org/rdp-ssh-tunnel/).
        
    - Enter your Windows server credentials when prompted.
        
4. Connect to RDP:
    
    - Initiate the RDP connection through the established SSH tunnel[4](https://paulbradley.org/rdp-ssh-tunnel/).
        

For a more user-friendly setup, you can use PuTTY on Windows[6](https://www.cloudthat.com/resources/blog/a-guide-to-access-rdp-through-ssh-tunneling-using-putty):

1. Configure PuTTY:
    
    - In PuTTY Configuration, go to Connection > SSH > Tunnels.
        
    - Set Source port to "3389" and Destination to "WINDOWS_SERVER_IP:3389".
        
    - Click "Add" to create the tunnel.
        
2. Establish the connection:
    
    - In PuTTY, enter the bastion host details and connect.
        
    - Keep the PuTTY window open to maintain the tunnel.
        
3. Use RDP:
    
    - Open Remote Desktop Connection.
        
    - Connect to "localhost:3389".
        
This setup allows secure RDP access to your Windows server through an SSH tunnel via the bastion host, enhancing security by not exposing the RDP port directly to the internet[7](https://security.theodo.com/en/blog/bastion-sshuttle).