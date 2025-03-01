# Security best practices for Systems Manager<a name="security-best-practices"></a>

AWS Systems Manager provides a number of security features to consider as you develop and implement your own security policies\. The following best practices are general guidelines and don’t represent a complete security solution\. Because these best practices might not be appropriate or sufficient for your environment, treat them as helpful considerations rather than prescriptions\. 

**Topics**
+ [Systems Manager preventative security best practices](#security-best-practices-prevent)
+ [Systems Manager monitoring and auditing best practices](#security-best-practices-detect)

## Systems Manager preventative security best practices<a name="security-best-practices-prevent"></a>

The following best practices for Systems Manager can help prevent security incidents\.

**Implement least privilege access**  
When granting permissions, you decide who is getting what permissions to which Systems Manager resources\. You allow specific actions that you want to allow on those resources\. Therefore you should grant only the permissions that are required to perform a task\. Implementing least privilege access is fundamental in reducing security risk and the impact that could result from errors or malicious intent\.   
The following tools are available to implement least privilege access:  
+ [IAM user policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html) and [Permissions boundaries for IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html)
+ [Service control policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)

**Use SecureString parameters to encrypt and protect secret data**  
In Parameter Store, a capability of AWS Systems Manager, a `SecureString` parameter is any sensitive data that needs to be stored and referenced in a secure manner\. If you have data that you don't want users to alter or reference in plaintext, such as passwords or license keys, create those parameters using the `SecureString` data type\. Parameter Store uses an AWS KMS key in AWS Key Management Service \(AWS KMS\) to encrypt the parameter value\. AWS KMS uses either a customer managed key or an AWS managed key when encrypting the parameter value\. For maximum security, we recommend using your own KMS key\. If you use the AWS managed key, any user with permission to run the [GetParameter](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_GetParameter.html) and [GetParameters](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_GetParameters.html) actions in your account can view or retrieve the content of all `SecureString` parameters\. If you're using customer managed keys to encrypt your secure `SecureString` values, you can use IAM policies and key policies to manage permissions for encrypting and decrypting parameters\. You can't establish access control policies for these operations when you use the customer managed keys\.  
For more information, see [Restricting access to Systems Manager parameters using IAM policies](sysman-paramstore-access.md) and [How AWS Systems Manager Parameter Store Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html) in the *AWS Key Management Service Developer Guide*\.

**Define allowedValues and allowedPattern for document parameters**  
You can validate user input for parameters in Systems Manager documents \(SSM documents\) by defining `allowedValues` and `allowedPattern`\. For `allowedValues`, you define an array of values allowed for the parameter\. If a user inputs a value that isn't allowed, the execution fails to start\. For `allowedPattern`, you define a regular expression that validates whether the user input matches the defined pattern for the parameter\. If the user input doesn't match the allowed pattern, the execution fails to start\.  
For more information about `allowedValues` and `allowedPattern`, see [SSM document data elements](sysman-doc-syntax.md)\.

**Block public sharing for documents**  
Unless your use case requires public sharing to be allowed, we recommend turning on the block public sharing setting for your SSM documents in the **Preferences** section of the Systems Manager Documents console\.

**Use an Amazon Virtual Private Cloud \(Amazon VPC\) and VPC endpoints**  
You can use Amazon VPC to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\.  
By implementing a VPC endpoint, you can privately connect your VPC to supported AWS services and VPC endpoint services powered by AWS PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service doesn't leave the Amazon network\.  
For more information about Amazon VPC security, see [\(Recommended\) Create a VPC endpoint](setup-create-vpc.md) and [Internetwork traffic privacy in Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html) in the *Amazon VPC User Guide*\.

**Restrict Session Manager users to sessions using interactive commands**  
Session Manager, a capability of AWS Systems Manager, provides [several methods for starting sessions](session-manager-working-with-sessions-start.md) to your managed nodes\. For the most secure connections, you can require users to connect using the *interactive commands* method to limit user interaction to a specific command or command sequence\. This helps you manage the interactive actions a user can take\. For more information, see [Starting a session \(interactive and noninteractive commands\)](session-manager-working-with-sessions-start.md#sessions-start-interactive-commands)\.

**Provide temporary node permissions for Automation workflows**  
During a workflow in Automation, a capability of AWS Systems Manager, your nodes might need permissions that are needed for that execution only but not for other Systems Manager operations\. For example, an Automation workflow might require a node to call a particular API operation or access an AWS resource specifically during the workflow\. If these calls or resources are ones that you want to limit access to, you can provide temporary, supplemental permissions for your nodes within the Automation runbook itself instead of adding the permissions to your IAM instance profile\. At the end of the Automation workflow, the temporary permissions are removed\. For more information, see [Providing temporary instance permissions with AWS Systems Manager Automations](http://aws.amazon.com/blogs/mt/providing-temporary-instance-permissions-with-aws-systems-manager-automations/) on the *AWS Management and Governance Blog*\.

**Keep AWS and Systems Manager tools up to date**  
AWS regularly releases updated versions of tools and plugins that you can use in your AWS and Systems Manager operations\. Keeping these resources up to date ensures that users and nodes in your account have access to the latest functionality and security features in these tools\.  
+ SSM Agent – AWS Systems Manager Agent \(SSM Agent\) is Amazon software that can be installed and configured on an Amazon Elastic Compute Cloud \(Amazon EC2\) instance, an on\-premises server, or a virtual machine \(VM\)\. SSM Agent makes it possible for Systems Manager to update, manage, and configure these resources\. We recommend checking for new versions, or automating updates to the agent, at least every two weeks\. For information, see [Automating updates to SSM Agent](ssm-agent-automatic-updates.md)\.
+ AWS CLI – The AWS Command Line Interface \(AWS CLI\) is an open source tool that allows you to interact with AWS services using commands in your command\-line shell\. To update the AWS CLI, you run the same command used to install the AWS CLI\. We recommend creating a scheduled task on your local machine to run the command appropriate to your operating system at least once every two weeks\. For information about installation commands, see [Installing the AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) in the *AWS Command Line Interface User Guide*\. 
+ AWS Tools for Windows PowerShell – The Tools for Windows PowerShell are a set of PowerShell modules that are built on the functionality exposed by the AWS SDK for \.NET\. The AWS Tools for Windows PowerShell allow you to script operations on your AWS resources from the PowerShell command line\. Periodically, as updated versions of the Tools for Windows PowerShell are released, you should update the version that you're running locally\. For information, see [Updating the AWS Tools for Windows PowerShell on Windows](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up-windows.html#pstools-updating) or [Updating the AWS Tools for Windows PowerShell on Linux or macOS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-getting-set-up-linux-mac.html#pstools-updating-linux) in the *IAM Policy Simulator User Guide*\.
+ Session Manager plugin – If users in your organization with permissions to use Session Manager want to connect to a node using the AWS CLI, they must first install the Session Manager plugin on their local machines\. To update the plugin, you run the same command used to install the plugin\. We recommend creating a scheduled task on your local machine to run the command appropriate to your operating system at least once every two weeks\. For information, see [\(Optional\) Install the Session Manager plugin for the AWS CLI](session-manager-working-with-install-plugin.md)\.
+ CloudWatch agent – You can configure and use the CloudWatch agent to collect metrics and logs from your EC2 instances, on\-premises instances, and virtual machines \(VMs\)\. These logs can be sent to Amazon CloudWatch Logs for monitoring and analysis\. We recommend checking for new versions, or automating updates to the agent, at least every two weeks\. For the simplest updates, use AWS Systems Manager Quick Setup\. For information, see [AWS Systems Manager Quick Setup](systems-manager-quick-setup.md)\. 

## Systems Manager monitoring and auditing best practices<a name="security-best-practices-detect"></a>

The following best practices for Systems Manager can help detect potential security weaknesses and incidents\.

**Identify and audit all your Systems Manager resources**  
Identification of your IT assets is a crucial aspect of governance and security\. You need to identify all of your Systems Manager resources to assess their security posture and take action on potential areas of weakness\.  
Use Tag Editor to identify security\-sensitive or audit\-sensitive resources, then use those tags when you need to search for these resources\. For more information, see [Find resources to tag](https://docs.aws.amazon.com/ARG/latest/userguide/find-resources-to-tag.html) in the *AWS Resource Groups User Guide*\.   
Create resource groups for your Systems Manager resources\. For more information, see [What are resource groups?](https://docs.aws.amazon.com/ARG/latest/userguide/resource-groups.html) 

**Implement monitoring using Amazon CloudWatch monitoring tools**  
Monitoring is an important part of maintaining the reliability, security, availability, and performance of Systems Manager and your AWS solutions\. Amazon CloudWatch provides several tools and services to help you monitor Systems Manager and your other AWS services\. For more information, see [Sending node logs to unified CloudWatch Logs \(CloudWatch agent\)](monitoring-cloudwatch-agent.md) and [Monitoring Systems Manager events with Amazon EventBridge](monitoring-eventbridge-events.md)\.

**Use CloudTrail**  
AWS CloudTrail provides a record of actions taken by a user, role, or an AWS service in Systems Manager\. Using the information collected by CloudTrail, you can determine the request that was made to Systems Manager, the IP address from which the request was made, who made the request, when it was made, and additional details\. For more information, see [Logging AWS Systems Manager API calls with AWS CloudTrail](monitoring-cloudtrail-logs.md)\.

**Turn on AWS Config**  
AWS Config allows you to assess, audit, and evaluate the configurations of your AWS resources\. AWS Config monitors resource configurations, allowing you to evaluate the recorded configurations against the desired secure configurations\. Using AWS Config, you can review changes in configurations and relationships between AWS resources, investigate detailed resource configuration histories, and determine your overall compliance against the configurations specified in your internal guidelines\. This can help you simplify compliance auditing, security analysis, change management, and operational troubleshooting\. For more information, see [Setting Up AWS Config with the Console](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html) in the *AWS Config Developer Guide*\. When specifying the resource types to record, ensure that you include Systems Manager resources\. 

**Monitor AWS security advisories**  
You should regularly check security advisories posted in Trusted Advisor for your AWS account\. You can do this programmatically using [describe\-trusted\-advisor\-checks](https://docs.aws.amazon.com/cli/latest/reference/support/describe-trusted-advisor-checks.html)\.  
Further, actively monitor the primary email address registered to each of your AWS accounts\. AWS will contact you, using this email address, about emerging security issues that might affect you\.  
AWS operational issues with broad impact are posted on the [AWS Service Health Dashboard](https://status.aws.amazon.com/)\. Operational issues are also posted to individual accounts through the Personal Health Dashboard\. For more information, see the [AWS Health Documentation](https://docs.aws.amazon.com/health/)\.

**More info**  
+ [Best Practices for Security, Identity, & Compliance](http://aws.amazon.com/architecture/security-identity-compliance/)
+ [Getting Started: Follow Security Best Practices as You Configure Your AWS Resources](http://aws.amazon.com/blogs/security/getting-started-follow-security-best-practices-as-you-configure-your-aws-resources/) \(AWS Security Blog\)
+ [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
+ [Security best practices in AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/best-practices-security.html)
+ [Security Best Practices for Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
+ [Security best practices for AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/best-practices.html)