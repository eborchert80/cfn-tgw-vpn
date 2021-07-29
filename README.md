# cfn-tgw-vpn
CloudFormation Templates to establish a site to site VPN with TGW or VGW
                VPN
<site1>-------------------<site2>
    34.1.1.1           34.2.2.2

<Run CF Template for Site1>
1. Run the CF Template for Site1 using dummy IP
2. TGW ASN: The CGW and TGW ASN needs to be different.  Site 1 can be 65001 and Site2 can be 65002 for this example
3. Fill in the Remote Cidr of Site2.
4. VPC Route Table ID is your VPC route table where site1 lives
5. Subnet ID is the subnet ID where VPN Gateway for Site1 lives
6. Document Tunnel Outside IP Address: https://eborchert.medium.com/site-to-site-s2s-vpn-between-aws-vgw-tgw-c27777257fa7

<Now Run CF Template for Site2>
1. For the CGW IP address use the value collected from step #6 of Site1

<Perform Manual Steps>
1. Set the startup Action to Start on Site1 side
aws ec2 modify-vpn-tunnel-options --vpn-connection-id vpn-<vpn connectoin id> --vpn-tunnel-outside-ip-address <tunnel outside IP that is being modified. see previous step> --tunnel-option StartupAction=start
2. Create a new CGW for Site2 and associate it with the Site1 to Site2 VPN
e.g,
 aws ec2 create-customer-gateway --bgp-asn 65001 --public-ip 34.1.1.1 --device-name Site1-CGW --type ipsec.1
 aws ec2  modify-vpn-connection  --vpn-connection-id <vpn-id-of-site1-to-site2> --customer-gateway-id <cgw-id created in previous step>

Once the config modifications take place you should see the First Tunnel Come up. Give it 5-10 minutes for AWS to complete the provisioning 