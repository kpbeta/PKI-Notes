#port_knocking #rdp 
Port knocking is a technique to enhance security for remote access services like RDP by keeping ports closed until a specific sequence of connection attempts is made. Here's how to implement port knocking for RDP:

1. Install a port knocking daemon:  
    On Linux, use `knockd`. For Windows, third-party tools like "Simple Port Knocker" can be used[1](https://github.com/vigisoft/rdp-port-knocking).
    
2. Configure the port knocking sequence:  
    Define a specific sequence of ports to be "knocked" before opening the RDP port. For example:
    
    `sequence = 7000,8000,9000`
    
    This sequence must be hit in order within a specified time frame[4](https://goteleport.com/blog/ssh-port-knocking/).
    
- Set up firewall rules:  
    Configure your firewall to block the RDP port (3389) by default. The port knocking daemon will dynamically open this port when the correct sequence is received[3](https://www.reddit.com/r/mikrotik/comments/d1disz/how_to_port_knocking_for_external_access/).
    
- Define open and close actions:  
    In the knockd configuration, specify commands to open and close the RDP port:

    
    `[openRDP] sequence = 7000,8000,9000 command = iptables -A INPUT -s %IP% -p tcp --dport 3389 -j ACCEPT [closeRDP] sequence = 9000,8000,7000 command = iptables -D INPUT -s %IP% -p tcp --dport 3389 -j ACCEPT`
    
    This opens the port for the specific IP that completed the knock sequence[4](https://goteleport.com/blog/ssh-port-knocking/).
    
2. Client-side implementation:  
    Use a port knocking client or script to send the knock sequence before attempting an RDP connection. For example, using `telnet` or `netcat` to hit the specified ports in sequence[5](https://cs.fyi/guide/port-knocking-explained).
    
3. Additional security measures:
    
    - Use non-standard ports for the knocking sequence to avoid easy detection[3](https://www.reddit.com/r/mikrotik/comments/d1disz/how_to_port_knocking_for_external_access/).
        
    - Implement a timeout for the open port to automatically close it after a set period[1](https://github.com/vigisoft/rdp-port-knocking).
        
    - Consider combining port knocking with other security measures like MFA for enhanced protection[7](https://serverfault.com/questions/424326/implementing-a-form-of-port-knocking-phone-factor-2-factor-auth-for-rdp).
        

By implementing port knocking, you add an extra layer of security to your RDP service, making it significantly harder for potential attackers to discover and exploit the open RDP port[5](https://cs.fyi/guide/port-knocking-explained)[8](https://forums.whirlpool.net.au/archive/603582).

References
* [https://github.com/vigisoft/rdp-port-knocking](https://github.com/vigisoft/rdp-port-knocking)
* [https://live.paloaltonetworks.com/t5/community-blogs/knock-knock-who-s-there/ba-p/417975](https://live.paloaltonetworks.com/t5/community-blogs/knock-knock-who-s-there/ba-p/417975)
* [https://www.reddit.com/r/mikrotik/comments/d1disz/how_to_port_knocking_for_external_access/](https://www.reddit.com/r/mikrotik/comments/d1disz/how_to_port_knocking_for_external_access/)