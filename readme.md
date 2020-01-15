# AWS bastion server

A bastion server will provide an extra layer of protection by not allowing direct SSH access to the private subnet, but rather the a user from the web can access into a private subnet and if they have the correct key and credentials can then SSH into the private subnet which can hold the app server and db server.

Since the app server is in the private subnet, it requires the nginx reverse proxy for it to be accessible through the public server.

- Security Group is stateful (remember where the request came from ), in which there is no need to specify outbound rules.
- NACL is stateless, in which there is a need to specify outbound rules explicitly. (E.G From Web --> Public , allow in : 1.SSH from web to public subnet and 2. ports 1024 -65535 from the private subnet to return the SSH request, and outbound will be 1. SSH from Public to private and 2. ports 1024 -65535 to return the SSH request to the web. )
(While the Public ----> Private has inbound rules SSH from public to private on port 22 and ports 1024 -65535 from private to public to return the ssh request, and outbound will be )

Ideally you need SSH inbound and the accompanied outbound which is usually port 1024-65535 and vice versa which will return the request. This needs to be stated in the NACL but not in the SG, in the SG the inbound is sufficient as the SG can 'remember' where the request came from and where to return it to.

## Bastion servers typically work by:
### Web ----SSH---> public subnet(NGINX reverse proxy server & Bastion Server)
### public subnet ---SSH----> private subnet (App/Web Server & DB Server)
Typically two types of rules for inbound, the SSH from web and the inbound rules to allow the SSH from the private subnet BACK to the public subnet.
And for outbound you may need a rule to allow it to SSH into the private subnet and a rule to allow it to return the SSH back to the web from the web.
If we have an SSH rule IN, we will likely need a SSH rule back OUT for network ACLs but not necessarily SGs
