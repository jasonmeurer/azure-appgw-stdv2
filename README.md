# Using VM-Series Firewalls, the Azure Application Gateway and Standard ILB to Secure Internet-Facing Web Workloads v2

Adapted from the [Azure Application Gateway ARM template](https://github.com/PaloAltoNetworks/azure-applicationgateway).

Instructions

0. Deploy the template, link below.
1. Make note of Internal LB - LoadBalancerFrontend IP
2. Make note of Firewall Unrust IP - Eth1
3. If SSH password was used, ssh to the firewall Public IP to set an admin password.
	* configure
	* set mgt-config users "insert admin name" password
	* commit
4. HTTPS to the Firewall's Public IP and Import/Load - fwconfig.xml
	* Adapted from Step 3 here. [Azure Template Deployment Proceedure](https://www.paloaltonetworks.com/documentation/71/virtualization/virtualization/set-up-the-vm-series-firewall-in-azure/start-using-the-vm-series-azure-application-gateway-template#_37860)
5. Update firewall-untrust-IP and internal-load-balancer-IP objects to match recorded addresses in steps 1 and 2 and commit.
6. If default subnets where not used, update the corresponding objects and Virtual Routers accordingly and commit.
7. Following the commits, credentials may be reset, use pandemo:demopassword to access the FW.
8. Relavent links
	* http://appGWip/wordpress
	* http://appGWip/sql-attack.html
		* Used for initiating traffic from Web to DB for visibility in the FW Monitor.
		* Once flows are verified, enable Threat prevention on the "eastwestpol" and relaunce the SQL attack.  Check the Monitor to validate the block.
	* http://appGWip/showheaders.php
		* Useful for troubleshooting XFF received by the web server.
9. Following along demo outlined here substituting the Application Gateway IP for the web server reference and keep in mind that you have more than one firewall to Monitor.
[Demo Guide](https://github.com/PaloAltoNetworks/azure/blob/master/two-tier-sample/Azure_ARM_template_deployment_guide.pdf)

This ARM template deploys two VM-Series firewalls between a pair of Azure load balancers. The external load balancer is an Azure Application Gateway (a web load balancer) that also serves as the Internet facing gateway, which  receives traffic and distributes it to the VM-Series firewalls. The firewalls enforce security policies to protect your workloads, and send the allowed traffic to the internal load balancer which is an Azure Load Balancer (Layer 4) that load balances across a pair of sample Apache web servers.  The ILB is utilizes a Standard Preview which can be used with HA Ports for Outbound and East/West traffic.

[Standard Load Balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-standard-overview)

[HA Ports](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-ha-ports-overview)

Standard Preview is available in the following regions.
* East US 2
* Central US
* North Europe
* West Central US
* West Europe
* Southeast Asia

The iLB has two Front End configurations.  The first load balances the web servers.  The second utilizes the HA Port feature coupled with UDRs to send Web to DB and DB to Web traffic through the firewall.

As demand for your web services increase, you can add more web servers and deploy additional VM-Series firewalls for more capacity. The VM-Series firewalls are deployed in separate Availability Sets for higher availability and redundancy against planned and unplanned outages. Refer to Azure documentation for more information on [Availability Sets](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-manage-availability). A sample configuration file for VM-Series firewall is also included. After you import this configuration file, be sure to (a) customize the security policies to your needs and (b) <b>set a custom password</b> for the firewall instead of the value in the sample file. Refer to the documentation for steps on how to import the sample configuration file. 

**Documentation**
* Release Notes: Included in this repository.
* About the [VM-Series Firewall for Azure](https://azure.paloaltonetworks.com)

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjasonmeurer%2Fazure-appgw-stdv2%2Fmaster%2Fazuredeploy.json)
