##Q1. The company wants to provision a brand new server from a third party provider for hosting a kubernetes cluster. By default, this server is provisioned with a Linux OS of your choice and ssh root access using your ssh key. Explain in detail the various security issues that will need to be addressed and how you would address them. What would be needed to ensure this server is hardened from various threats? What kind of threats would we need to be wary of? Include the OS, any specific tools, commands, caveats and reasoning behind your answer.

Though it looks like a simple and straight forward question. This task needs to be addressed from multiple angles. I could say that we can do a bunch of hardening steps like updating the os to latest, setting up password complexity and aging,turnoff ssh root authentication, turning of the ICMPs, configure firewalls/tcpwrappers etc.. However, it would be better to define an origination/app wide governance policy. Such that we can define standards, make a repeatable process also we should have capability to compare the current environment vs standards in any point in time. With the help of scripts or tools we can apply/compare/enforce this standard to any new or existing server. This way we can have uniform set of servers across fleet as well as reduce provisioning time. 

I would start with creating controls for below major points, 

**1.	Physical security controls:** This defines the minimum physical security requirements to be followed in the Data center. Disabling unused peripherals like USB/Fiberchannel slots, enabling BIOS passwords are examples.Also this will takes care of data destruction part of decommissioned servers.  
     
2.	OS hardening: There are 100+ parameters to be configured based on the workload, services and organization needs this will be defined and applied. For example, NFS related security policies to be enabled only when there are NFS services enabled.  
3.	Authentication/Authorization management: Define who all can authenticate to the server and define what authorization they have on servers based on the roles. Consider RBAC like sudo/freeipa. Also define procedures for timely offboarding of users when they leave team/org.  
4.	Vulnerability management: Define a VM framework adapt tools like nessus/opnvas/snort and routinely scan the environment. Create vulnerability remediation policies.   
5.	Patch management: Define when and from where to apply patches. Create patch groups and cooldown times for minimal/void service interruptions. 
6.	Compliance management. ( On demand basis )  controls for GDPR guide lines.Sox compliance etc.. 

Being that said, automated continues security with minimal efforts is the key.     




