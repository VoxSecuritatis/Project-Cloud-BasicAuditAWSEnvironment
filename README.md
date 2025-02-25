# Project:  Performing a Basic Audit of an AWS Environment

## DESCRIPTION

This project goes through the steps to perform basic audits of core AWS resources. It will use the AWS Management Console to understand how to audit the use of multiple AWS services, Amazon EC2, Amazon VPC, Amazon IAM, Amazon Security Groups, AWS CloudTrail and AWS CloudWatch. This project will help one to understand how they can extend existing auditing objectives related to organizational Governance, Asset Configuration, Logical Access Controls, Operating Systems, Databases and Applications security configurations within AWS. The skills learned help to provide visibility, testability and automated audit evidence gather capabilities.

## OBJECTIVES

- Review user permissions in AWS IAM
- Capture audit evidence using AWS IAM Policy Simulator
- Review Inbound and Outbound networking rules for Amazon EC2 Security Groups
- Review Amazon VPC configurations, subnets, and Network ACLs
- Review Amazon CloudWatch performance metrics
- Review raw Amazon CloudTrail logs within Amazon S3

## AWS SERVICES USED

- Amazon Elastic Compute Cloud (Amazon EC2)
- Amazon Virtual Private Cloud (Amazon VPC)
- AWS CloudTrail
- AWS CloudWatch
- AWS Identity and Access Management (IAM)

## Task 1: Audit user permissions in IAM

AWS Identity and Access Management (IAM) enables the engineer to securely control access to AWS services and resources for end users. Using IAM, the engineer can create and manage AWS users and groups and use permissions to allow and deny their access to AWS resources.

In this project, the auditor will launch IAM secure AWS Access Control in order to review permissions, group assignments and roles associated with the auditing instance:

Launch the AWS Console:

<img src="https://imgur.com/4znV30l.jpg" height="80%" width="80%" alt="Open AWS Console"/>

### Review permissions

1. At the top of the AWS Management Console, in the search bar, search for and choose IAM.

<img src="https://imgur.com/UwO7osv.jpg" height="80%" width="80%" alt="Open IAM Console"/>

2. In the navigation pane at the left of the page, under Access management, choose Users.

<img src="https://imgur.com/zFDjtUM.jpg" height="80%" width="80%" alt="Select Users"/>

3. On the Users page, choose the link for user-1 to view its details.
4. Review the Summary section for information about your user.

<img src="https://imgur.com/MFVjt7G.jpg" height="80%" width="80%" alt="User information"/>

5. Select the Security credentials tab to review it. Here, the auditor can see how many access keys a user has, when an access key was created, whether a Multi-Factor Authentication (MFA) device is assigned, and more.

   - **Access Keys**: Keys can be active or inactive and only administrators have the ability to inactivate or delete keys. If a user with limited permissions tried to deactivate or delete an access key, they would receive a message similar to the following:

We encountered the following errors while processing the request: User:arn:aws:iam::222244443333:user/user-1_01 is not authorized to perform: iam:UpdateAccessKey on resource: user user-1_01

   - **Signing Certificates**: Can be signed certificates, X.509 Certificate and/or third party tools (e.g. OpenSSL).
   - **Console password**: Users with access to the AWS Management Console require a password. Passwords can be generated and/or changed by administrators within the IAM dashboard. Passwords can be auto-generated or custom-generated based on organization preferences.
   - **Assigned MFA (Multi-Factor Authentication) Device**: Multi-Factor Authentication is a simple best practice that adds an extra layer of protection on top of your username and password.
     - **Virtual**: Use your existing smartphone, tablet, or computer running any application that supports the open TOTP standard.
     - **Hardware Keyfob**: Tamper-evident hardware keyfob device provided by Gemalto, a 3rd-party provider.
     - **Hardware Display Card**: Tamper-evident hardware display card device provided by Gemalto, a 3rd-party provider.

<img src="https://imgur.com/UI9vmoY.jpg" height="80%" width="80%" alt="Review Security Credentials 1"/>
<img src="https://imgur.com/yC0wpSp.jpg" height="80%" width="80%" alt="Review Security Credentials 2"/>

6. Choose the Groups tab.

<img src="https://imgur.com/WYktLDZ.jpg" height="80%" width="80%" alt="Review User Groups"/>
<img src="https://imgur.com/T8LkuRX.jpg" height="80%" width="80%" alt="Review Group Permissions"/>

### Run the IAM Policy Simulator

The auditor can use the IAM Policy Simulator to test the effects of AWS IAM policies to test existing IAM policies to verify that they have the intended effect and capture the Policy Simulator output to use as supporting evidence in user access reviews.

7. Choose the Permissions tab. In the Permissions policies section, notice there is one policy attached to the user. The Attached via column shows that the ReadOnlyAccess policy is attached to user-1 via the user1group IAM group.
8. To run the IAM Policy Simulator, open the following link in a new web browser tab: [IAM Policy Simulator](https://policysim.aws.amazon.com/home/index.jsp?#).

<img src="https://imgur.com/YF4agZE.jpg" height="80%" width="80%" alt="Launch IAM Policy Simulator"/>

9. On the IAM Policy Simulator page, in the Users, Groups, and Roles pane, choose user-1.
10. In the Policy Simulator pane, on the Select service drop-down menu, choose Identity and Access Management.
11. On the Select actions drop-down menu, select the following options:
    - DeleteGroup
    - DeleteRolePolicy

<img src="https://imgur.com/cj1xvW2.jpg" height="80%" width="80%" alt="Policy Simulator Audit"/>


12. Choose Run Simulation.

<img src="https://imgur.com/6KZqGzF.jpg" height="80%" width="80%" alt="Policy Simulator Run"/>

> Expected output: The Action Settings and Results section displays the effective permissions for user-1, similar to this:

| Service                            | Action           | Resource Type | Simulation Resource | Permission                                         |
|------------------------------------|------------------|---------------|---------------------|----------------------------------------------------|
| AWS Identity and Access Management | DeleteGroup      | group         | *                   | denied Implicitly denied (no matching statements). |
| AWS Identity and Access Management | DeleteRolePolicy | role          | *                   | denied Implicitly denied (no matching statements). |

<img src="https://imgur.com/E6Wwcyr.jpg" height="80%" width="80%" alt="Policy Simulator Results"/>

13. Close the IAM Policy Simulator web browser tab.

### Collecting audit evidence

From an audit evidence standpoint, the auditor can capture the IAM settings and the IAM Policy Simulator output to be used as support evidence in user access reviews.

## Task 2: Review the security configuration of Amazon EC2 instances

### What is Amazon EC2?

Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides resizable compute capacity in the cloud. It is designed to make web-scale computing easier for developers. Amazon EC2 presents a true virtual computing environment, allowing the organization to use web service interfaces to launch instances with a variety of operating systems, load them with a custom application environment, manage network’s access permissions, and run an image using as many or few systems as one’s desire.

### What is a security group?

A security group acts as a virtual firewall for your instance to control inbound and outbound traffic. Security groups act at the instance level, not the subnet level. For each security group, the engineer adds rules that control the inbound traffic to instances and a separate set of rules that control the outbound traffic.

The following are basic characteristics of security groups:

- Can specify allow rules, but not deny rules.
- Can specify separate rules for inbound and outbound traffic.
- By default, no inbound traffic is allowed until the engineer adds inbound rules to the security group.
- By default, all outbound traffic is allowed until the engineer adds outbound rules to the group. Then, specify the outbound traffic that is allowed.
- Responses to allowed inbound traffic are allowed to flow outbound regardless of outbound rules and vice versa, as security groups are therefore stateful.
- Instances associated with a security group can’t talk to each other unless the engineer adds rules allowing it.
  - Exception: The default security group has these rules by default.
- After the engineer launches an instance, they can change which security groups the instance is associated with.

### Review running Amazon EC2 instances

14. At the top of the AWS Management Console, in the search bar, search for and choose EC2.
15. In the navigation pane at the left of the page, under Instances, choose Instances. In this project environment, there are three running instances: Web Server, Bastion Host, and SQL Server.

### Review the web server security group

16. In the navigation pane at the left of the page, under Network & Security, choose Security Groups.
17. Select WebServerSG.
18. In the details pane at the bottom of the page, choose the Inbound rules tab.
19. Review the Inbound rules.

> Note: The engineer can specify a number of different sources in security group rules, such as anywhere, a custom IP address or CIDR, My IP (the IP address of your current workstation), or specific security groups. The rules chosen to implement are a critical step towards running instances and services within Amazon EC2.

### Review the bastion host security group

20. Clear WebServerSG.
21. Select BastionSG.

A bastion host is a special-purpose server on a network specifically designed and configured to withstand attacks. The computer generally hosts a single application (such as a proxy server) and all other services are removed or limited to reduce threats to the computer. It is hardened in this manner primarily due to its location and purpose, which is typically on the outside of the firewall and usually involves access from untrusted networks or computers.

22. To review the inbound and outbound rules, choose the Inbound rules and Outbound rules tabs respectively.

### Review the SQL server security group

23. Clear BastionSG.
24. Select SQLSG.
25. To review the inbound rules, choose the Inbound rules tab. Notice that the inbound rules are configured with a custom source—a security group ID from this account.
26. To review the outbound rules, choose the Outbound rules tab.

### Collecting audit evidence

From an audit evidence standpoint, these findings can support resource access isolation and data protection from internal or external threats. All access to the SQL Server instance is restricted via a jump box (Bastion Host); therefore, no internal user has direct access to it. Externally, the SQL Server only communicates with the web service via the WebServerSG and SQLSG security groups.

## Task 3: Review Amazon VPC security configurations

### What is Amazon VPC?

Amazon Virtual Private Cloud (Amazon VPC) permits the organization to launch AWS resources into a virtual network that the engineer has defined. This virtual network closely resembles a traditional network that would operate in an organization’s data center, with the benefits of using the scalable infrastructure of AWS. The engineer has complete control over their virtual networking environment, including selection of an organization’s IP address range, creation of subnets, and configuration of route tables and network gateways.

Amazon VPC provides two features that can be used to increase security for a VPC:
- **Security Groups**: Act as a firewall for associated Amazon EC2 instances, controlling both inbound and outbound traffic at the instance level.
- **Network Access Control Lists (ACLs)**: Act as a firewall for associated subnets, controlling both inbound and outbound traffic at the subnet level.

When the engineer launches an instance in a VPC, they can associate one or more security groups that they have created. Each instance in the VPC could belong to a different set of security groups. If the engineer does not specify a security group when they launch an instance, the instance automatically belongs to the default security group for the VPC.

### Locate Amazon EC2 instance VPC configurations

27. In the navigation pane at the left of the page, under Instances, choose Instances.
28. Select Web Server. The Details pane appears below the list of instances that shows information about the instance you selected.
29. In the Instance summary section, locate the VPC ID value and copy it to your favorite text editor.
   
> Note: The VPC ID should look similar to: `vpc-0385934dd2bef2354`. Every VPC is associated with a VPC ID. In the next section, you identify the VPC that is associated with this VPC ID.

### Review existing VPCs, subnets, and NACLs

In this section, the auditor reviews existing VPCs, subnets, and Network ACL capabilities within a Virtual Private Cloud.

30. At the top of the AWS Management Console, in the search bar, search for and choose VPC.
31. In the navigation pane at the left of the page, under Virtual private cloud, choose Your VPCs.
32. Select Lab VPC. The Details pane appears below the list of VPCs that shows the configuration elements for the selected VPC.
33. Notice that the VPC ID value is the same VPC ID value that was copied to your text editor.
34. In the Details section, choose the Main network ACL link.
35. On the Network ACLs page, select the Network ACL which has a Default parameter value of Yes.
   
> Note: There should only be one choice.

36. To review the inbound and outbound rules, in the Details pane at the bottom of the page, choose the Inbound rules and Outbound rules tabs respectively.
   
> Note: As audit evidence, you can see how the VPC is using ACLs to communicate with an external network via explicit protocols.

## Task 4: Audit CloudWatch metrics and alarms

In this task, the auditor reviews built-in CloudWatch metrics, alarms, and service health associated with running instances, storage volumes, and data services within the auditing instance.

### What is Amazon CloudWatch?

Amazon CloudWatch is a monitoring and management service built for developers, system operators, site reliability engineers (SREs), and IT managers. CloudWatch provides an organization with data and actionable insights to monitor their applications, understand and respond to system-wide performance changes, optimize resource utilization, and get a unified view of operational health. CloudWatch collects monitoring and operational data in the form of logs, metrics, and events, providing the organization with a unified view of AWS resources, applications and services that run on AWS, and on-premises servers. The organization can use CloudWatch to set high-resolution alarms, visualize logs and metrics side by side, take automated actions, troubleshoot issues, and discover insights to optimize your applications, and ensure they are running smoothly.

### Audit CloudWatch metrics and alarms

37. At the top of the AWS Management Console, in the search bar, search for and choose CloudWatch.
38. In the navigation pane at the left of the page, in the Metrics section, choose All metrics.
39. On the Browse tab, choose EC2.
40. Choose Per-Instance Metrics.
41. In the Search box, search for CPUUtilization.
   
> Expected output: The search results should display the three EC2 instance that you reviewed previously.

42. Select SQL Server.
43. Choose the Graphed metrics tab.
   
> Note: You can change the Statistic and the Period settings to customize the view to your liking.

### Review CloudWatch data for EBS volumes

In addition to viewing Amazon CloudWatch metrics and alarms via the CloudWatch dashboard, one can also view the data in other locations. In this section, the auditor will review Amazon CloudWatch data for Amazon EBS volumes.

44. At the top of the AWS Management Console, in the search bar, search for and choose EC2.
45. In the navigation pane at the left of the page, under Elastic Block Store, choose Volumes.
46. Select the Volume that is attached to the Web Server instance.
   
> Note: To review the volume names and their attached instances, one might need to scroll to the right until they see the Attached Instances column header.

47. In the details pane at the bottom of the page, choose the Monitoring tab.
48. Review the CloudWatch metrics and any configured CloudWatch alarms.
   
> Note: Amazon CloudWatch metrics can directly support several auditing elements and provide real-time audit evidence based on predefined criteria and custom criteria related to organization processes.

## Task 5: Audit CloudTrail logs

### What is AWS CloudTrail?

AWS CloudTrail is a service that enables governance, compliance, operational auditing, and risk auditing of an AWS account. With CloudTrail, one can log, continuously monitor, and retain account activity related to actions across the organization’s AWS infrastructure. CloudTrail provides event history of the AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command line tools, and other AWS services. This event history simplifies security analysis, resource change tracking, and troubleshooting.

### Find CloudTrail logs

49. At the top of the AWS Management Console, in the search bar, search for and choose CloudTrail.
50. In the navigation pane at the left of the page, choose Trails.
51. Choose the LabCloudTrail link to view its details.
52. Review the CloudTrail configuration details.
   
> Note: The LabCloudTrail trail was created using AWS CloudFormation when you started the project. It’s configured to store logs in the Amazon S3 bucket with a name that starts with SPL73Logs, as defined by the Trail log location property.

53. At the top of the AWS Management Console, in the search bar, search for and choose S3.
54. Choose the link for the bucket name that starts with spl73logs.
55. Choose the AWSLogs/ link.
56. Continue selecting the links for the various folders until you get to a folder that represents the region your project was launched in.
   
> Note: The Region value is listed to the left of these instructions.

57. Continue selecting the links for the various folders, which represent today’s date, until you see a log file. The log file name contains the AWS account number, AWS region, a numeric representation of the day’s date and time, and a unique identifier, similar to this: `000044446666_CloudTrail_us-east-1_20250225T1935Z_ZBBrnLsySt8ZdwGnQ.json.gz`.
58. Choose the link for one of the log files, with a file name that ends in `json.gz`.
59. Choose Open.
   
> Expected output: Depending on web browser settings, a new window or a new tab opens that displays the contents of the log file. It is in JSON format.

An alternate approach to viewing Amazon CloudTrail logs is to download them locally and use a text editor along with the JSON Viewer plug-in.

### 3rd Party Solutions

AWS partners with third-party specialists in logging and analysis to provide solutions that leverage Amazon CloudTrail output, such as Splunk or Alert Logic.

## Conclusion

The following actions were taken:
- Reviewed user permissions in AWS IAM.
- Captured audit evidence using AWS IAM Policy Simulator.
- Reviewed Inbound and Outbound networking rules for Amazon EC2 Security Groups.
- Reviewed Amazon VPC configurations, subnets, and Network ACLs.
- Reviewed Amazon CloudWatch performance metrics.
- Reviewed raw Amazon CloudTrail logs within Amazon S3.

## End project

Follow these steps to close the console and end your project.

60. Return to the AWS Management Console.
61. At the upper-right corner of the page, choose the active user, and then choose Sign out.
62. This completes the project.

## Additional Resources

- [Testing IAM policies with the IAM policy simulator](https://aws.amazon.com/iam/policy-simulator/)
- [AWS Security Center](https://aws.amazon.com/security/)
