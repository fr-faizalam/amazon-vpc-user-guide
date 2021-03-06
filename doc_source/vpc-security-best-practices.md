# Security Best Practices for Your VPC<a name="vpc-security-best-practices"></a>

 The following best practices are general guidelines and don’t represent a complete security solution\. Because these best practices might not be appropriate or sufficient for your environment, treat them as helpful considerations rather than prescriptions\.

The following are general best practices:
+ Use multiple Availability Zone deployments so you have high availability\.
+ Use security groups and Network ACLs\.
+ Use IAM policies to control access\.
+ Use Amazon CloudWatch to monitor your VPCs and VPN connections\.
+ When you create a network access control list \(ACL\), number the rules so that you have room for additions, For example, use 100, 200, and 300 as rules\. For more information, see [Network ACL Rules](vpc-network-acls.md#nacl-rules)\.

## Additional Resources<a name="seccurity-best-practices-additional-resources"></a>
+ [VPC Security Capabilities](https://aws.amazon.com/answers/networking/vpc-security-capabilities/)

## Recommended Network ACL Rules for Your VPC<a name="vpc-recommended-nacl-rules"></a>

The VPC wizard helps you implement common scenarios for Amazon VPC\. If you implement these scenarios as described in the documentation, you use the default network access control list \(ACL\), which allows all inbound and outbound traffic\. If you need an additional layer of security, you can create a network ACL and add rules\. For more information, see [Network ACLs](vpc-network-acls.md)\.

We recommend the following rules for each scenario\.

**Topics**<a name="nacl-rule-considerations"></a>

**Considerations**
+ We use the ephemeral port range 32768\-65535 as an example, or 1024\-65535 for a NAT gateway\. You should select a range that is appropriate for your configuration\. For more information, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.
+ If the maximum transmission unit \(MTU\) between hosts in your subnets is different, you must add the following rule, both inbound and outbound\. This ensures that Path MTU Discovery can function correctly and prevent packet loss\. Select **Custom ICMP Rule** for the type and **Destination Unreachable**, **fragmentation required, and DF flag set** for the port range \(type 3, code 4\)\. If you use traceroute, also add the following rule: select **Custom ICMP Rule** for the type and **Time Exceeded**, **TTL expired transit** for the port range \(type 11, code 0\)\. For more information, see [Network Maximum Transmission Unit \(MTU\) for Your EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/network_mtu.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Recommended Rules for a VPC with a Single Public Subnet<a name="nacl-rules-scenario-1"></a>

Use these rules when you chose the option in the Amazon VPC Console Wizard to create a single subnet with instances that can receive and send internet traffic\. For more information, see [VPC with a Single Public Subnet](VPC_Scenario1.md)\.

The following table shows the rules that we recommend\. They block all traffic except that which is explicitly required\.


|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv4 address\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv4 address\.  | 
|  120  |  Public IPv4 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from your home network \(over the internet gateway\)\.  | 
|  130  |  Public IPv4 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from your home network \(over the internet gateway\)\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  120  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 

### Recommended Rules for IPv6<a name="vpc-nacls-scenario-1-ipv6"></a>

If you implemented IPv6 support and created a VPC and subnet with associated IPv6 CIDR blocks, you must add separate rules to your network ACL to control inbound and outbound IPv6 traffic\. 

The following are the IPv6\-specific rules for your network ACL \(which are in addition to the preceding rules\)\.


|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv6 address\.  | 
|  160  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv6 address\.  | 
|  170  |  IPv6 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from your home network \(over the internet gateway\)\.  | 
|  180  |  IPv6 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from your home network \(over the internet gateway\)\.  | 
|  190  |  ::/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  130  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  140  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  150  |  ::/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 

## Recommended Rules for a VPC with Public and Private Subnets \(NAT\)<a name="nacl-rules-scenario-2"></a>

Use these rules when you chose the option in the Amazon VPC Console Wizard to create a public subnet with instances that can receive and send internet traffic, and a private subnet that can't receive traffic directly from the internet\. However, it can initiate traffic to the internet \(and receive responses\) through a NAT gateway or NAT instance in the public subnet\. For more information, see [VPC with Public and Private Subnets \(NAT\)](VPC_Scenario2.md)\.

For this scenario, you have a network ACL for the public subnet, and a separate network ACL for the private subnet\. The following table shows the rules that we recommend for each ACL\. They block all traffic except that which is explicitly required\. They mostly mimic the security group rules for the scenario\.


**ACL Rules for the Public Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv4 address\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv4 address\.  | 
|  120  |  Public IP address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from your home network \(over the internet gateway\)\.  | 
|  130  |  Public IP address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from your home network \(over the internet gateway\)\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  120  |  10\.0\.1\.0/24  |  TCP  |  1433  |  ALLOW  |  Allows outbound MS SQL access to database servers in the private subnet\.  This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  150  | 10\.0\.1\.0/24 | TCP | 22 | ALLOW |  Allows outbound SSH access to instances in your private subnet \(from an SSH bastion, if you have one\)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 


**ACL Rules for the Private Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  10\.0\.0\.0/24  |  TCP  |  1433  |  ALLOW  |  Allows web servers in the public subnet to read and write to MS SQL servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  120  |  10\.0\.0\.0/24  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from an SSH bastion in the public subnet \(if you have one\)\.  | 
|  130  |  10\.0\.0\.0/24  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from the Microsoft Terminal Services gateway in the public subnet\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from the NAT device in the public subnet for requests originating in the private subnet\. For information about specifying the correct ephemeral ports, see the important note at the beginning of this topic\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all IPv4 inbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  120  |  10\.0\.0\.0/24  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to the public subnet \(for example, responses to web servers in the public subnet that are communicating with DB servers in the private subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 

### Recommended Rules for IPv6<a name="vpc-nacls-scenario-2-ipv6"></a>

If you implemented IPv6 support and created a VPC and subnets with associated IPv6 CIDR blocks, you must add separate rules to your network ACLs to control inbound and outbound IPv6 traffic\. 

The following are the IPv6\-specific rules for your network ACLs \(which are in addition to the preceding rules\)\.


**ACL Rules for the Public Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv6 address\.  | 
|  160  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv6 address\.  | 
|  170  |  IPv6 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic over IPv6 from your home network \(over the internet gateway\)\.  | 
|  180  |  IPv6 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic over IPv6 from your home network \(over the internet gateway\)\.  | 
|  190  |  ::/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  160  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  170  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet  | 
|  180  |  2001:db8:1234:1a01::/64  |  TCP  |  1433  |  ALLOW  |  Allows outbound MS SQL access to database servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  200  |  ::/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  210  |  2001:db8:1234:1a01::/64  |  TCP  |  22  |  ALLOW  |  Allows outbound SSH access to instances in your private subnet \(from an SSH bastion, if you have one\)\.  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 


**ACL Rules for the Private Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  2001:db8:1234:1a00::/64  |  TCP  |  1433  |  ALLOW  |  Allows web servers in the public subnet to read and write to MS SQL servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  170  |  2001:db8:1234:1a00::/64  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from an SSH bastion in the public subnet \(if applicable\)\.  | 
|  180  |  2001:db8:1234:1a00::/64  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from a Microsoft Terminal Services gateway in the public subnet, if applicable\.  | 
|  190  |  ::/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from the egress\-only internet gateway for requests originating in the private subnet\.  This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  130  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  140  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  150  |  2001:db8:1234:1a00::/64  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to the public subnet \(for example, responses to web servers in the public subnet that are communicating with DB servers in the private subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 

## Recommended Rules for a VPC with Public and Private Subnets and AWS Site\-to\-Site VPN Access<a name="nacl-rules-scenario-3"></a>

Use these rules when you chose the option in the Amazon VPC Console Wizard to create a public subnet with instances that can receive and send internet traffic, and a VPN\-only subnet with instances that can communicate only with your home network over the AWS Site\-to\-Site VPN connection\. For more information, see [VPC with Public and Private Subnets and AWS Site\-to\-Site VPN Access](VPC_Scenario3.md)\.

For this scenario you have a network ACL for the public subnet, and a separate network ACL for the VPN\-only subnet\. The following table shows the rules that we recommend for each ACL\. They block all traffic except that which is explicitly required\.


**ACL Rules for the Public Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic to the web servers from any IPv4 address\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic to the web servers from any IPv4 address\.  | 
|  120  |  Public IPv4 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic to the web servers from your home network \(over the internet gateway\)\.  | 
|  130  |  Public IPv4 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic to the web servers from your home network \(over the internet gateway\)\.   | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound IPv4 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  0\.0\.0\.0/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  110  |  0\.0\.0\.0/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  120  |  10\.0\.1\.0/24  |  TCP  |  1433  |  ALLOW  |  Allows outbound MS SQL access to database servers in the VPN\-only subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  140  |  0\.0\.0\.0/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound IPv4 responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 


**ACL Settings for the VPN\-Only Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  10\.0\.0\.0/24  |  TCP  |  1433  |  ALLOW  |  Allows web servers in the public subnet to read and write to MS SQL servers in the VPN\-only subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  120  |  Private IPv4 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic from the home network \(over the virtual private gateway\)\.  | 
|  130  |  Private IPv4 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic from the home network \(over the virtual private gateway\)\.  | 
|  140  |  Private IP address range of your home network  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from clients in the home network \(over the virtual private gateway\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  Private IP address range of your home network  |  All  |  All  |  ALLOW  |  Allows all outbound traffic from the subnet to your home network \(over the virtual private gateway\)\. This rule also covers rule 120\. However, you can make this rule more restrictive by using a specific protocol type and port number\. If you make this rule more restrictive, you must include rule 120 in your network ACL to ensure that outbound responses are not blocked\.   | 
|  110  |  10\.0\.0\.0/24  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to the web servers in the public subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  120  |  Private IP address range of your home network  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients in the home network \(over the virtual private gateway\)\.  This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 

### Recommended Rules for IPv6<a name="vpc-nacls-scenario-3-ipv6"></a>

If you implemented IPv6 support and created a VPC and subnets with associated IPv6 CIDR blocks, you must add separate rules to your network ACLs to control inbound and outbound IPv6 traffic\. 

The following are the IPv6\-specific rules for your network ACLs \(which are in addition to the preceding rules\)\.


**ACL Rules for the Public Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows inbound HTTP traffic from any IPv6 address\.  | 
|  160  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows inbound HTTPS traffic from any IPv6 address\.  | 
|  170  |  IPv6 address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic over IPv6 from your home network \(over the internet gateway\)\.  | 
|  180  |  IPv6 address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic over IPv6 from your home network \(over the internet gateway\)\.  | 
|  190  |  ::/0  |  TCP  |  1024\-65535  |  ALLOW  |  Allows inbound return traffic from hosts on the internet that are responding to requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  ::/0  |  TCP  |  80  |  ALLOW  |  Allows outbound HTTP traffic from the subnet to the internet\.  | 
|  160  |  ::/0  |  TCP  |  443  |  ALLOW  |  Allows outbound HTTPS traffic from the subnet to the internet\.  | 
|  170  |  2001:db8:1234:1a01::/64  |  TCP  |  1433  |  ALLOW  |  Allows outbound MS SQL access to database servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  190  |  ::/0  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients on the internet \(for example, serving webpages to people visiting the web servers in the subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 


**ACL Rules for the VPN\-only Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  150  |  2001:db8:1234:1a00::/64  |  TCP  |  1433  |  ALLOW  |  Allows web servers in the public subnet to read and write to MS SQL servers in the private subnet\. This port number is an example only\. Other examples include 3306 for MySQL/Aurora access, 5432 for PostgreSQL access, 5439 for Amazon Redshift access, and 1521 for Oracle access\.  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  130  |  2001:db8:1234:1a00::/64  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to the public subnet \(for example, responses to web servers in the public subnet that are communicating with DB servers in the private subnet\)\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 

## Recommended Rules for a VPC with a Private Subnet Only and AWS Site\-to\-Site VPN Access<a name="nacl-rules-scenario-4"></a>

Use these rules when you chose the option in the Amazon VPC Console Wizard to create a single subnet with instances that can communicate only with your home network over an AWS Site\-to\-Site VPN connection\. For more information, see [VPC with a Private Subnet Only and AWS Site\-to\-Site VPN Access](VPC_Scenario4.md)\.

The following table shows the rules that we recommend\. They block all traffic except that which is explicitly required\.


|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  Private IP address range of your home network  |  TCP  |  22  |  ALLOW  |  Allows inbound SSH traffic to the subnet from your home network\.  | 
|  110  |  Private IP address range of your home network  |  TCP  |  3389  |  ALLOW  |  Allows inbound RDP traffic to the subnet from your home network\.  | 
|  120  |  Private IP address range of your home network  |  TCP  |  32768\-65535  |  ALLOW  |  Allows inbound return traffic from requests originating in the subnet\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all inbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  100  |  Private IP address range of your home network  |  All  |  All  |  ALLOW  |  Allows all outbound traffic from the subnet to your home network\. This rule also covers rule 120\. However, you can make this rule more restrictive by using a specific protocol type and port number\. If you make this rule more restrictive, you must include rule 120 in your network ACL to ensure that outbound responses are not blocked\.   | 
|  120  |  Private IP address range of your home network  |  TCP  |  32768\-65535  |  ALLOW  |  Allows outbound responses to clients in the home network\. This range is an example only\. For information about choosing the correct ephemeral ports for your configuration, see [Ephemeral Ports](vpc-network-acls.md#nacl-ephemeral-ports)\.  | 
|  \*  |  0\.0\.0\.0/0  |  all  |  all  |  DENY  |  Denies all outbound traffic not already handled by a preceding rule \(not modifiable\)\.  | 

### Recommended Rules for IPv6<a name="vpc-nacls-scenario-4-ipv6"></a>

If you implemented scenario 4 with IPv6 support and created a VPC and subnet with associated IPv6 CIDR blocks, you must add separate rules to your network ACL to control inbound and outbound IPv6 traffic\. 

In this scenario, the database servers cannot be reached over the VPN communication via IPv6, therefore no additional network ACL rules are required\. The following are the default rules that deny IPv6 traffic to and from the subnet\.


**ACL Rules for the VPN\-only Subnet**  

|  | 
| --- |
|  Inbound  | 
|  Rule \#  |  Source IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  \*  |  ::/0  |  all  |  all  |  DENY  |  Denies all inbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 
|  Outbound  | 
|  Rule \#  |  Dest IP  |  Protocol  |  Port  |  Allow/Deny  |  Comments  | 
|  \*  | ::/0 |  all  |  all  |  DENY  |  Denies all outbound IPv6 traffic not already handled by a preceding rule \(not modifiable\)\.  | 