#mfa #bastion #rdp #windows #port_knocking 
There are two ways:
1. Bastion Host
2. MFA: This is the difficult way if the servers are NOT AD joined

Additional Layers of Security for above 2 mentioned:
1. Remote Desktop Gateway (RDG): Deploy an RDG farm and integrate it with Azure AD using Network Policy Server (NPS) extension for MFA [See here](https://serverfault.com/questions/1034810/windows-server-2016-multi-factor-authentication-for-rdp-with-azure-ad)
2. Certificate Based Authentication
3. Obscure port for the log on service
4. Port Knocking: It is very transparent and detectable in logs and on passive listening
## My Recommendation
Bastion Host with Port Knocking

See [[How to setup a SSH tunneled bastion host for RDP]] for more details
Also see [[How to implement port knocking for RDP]] and [[What are the best of the softwares to setup port knocking?]]
## Note and Explanation on using MFA:
