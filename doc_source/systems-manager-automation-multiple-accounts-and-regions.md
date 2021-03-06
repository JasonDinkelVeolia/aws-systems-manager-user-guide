# Executing Automations in Multiple AWS Regions and Accounts<a name="systems-manager-automation-multiple-accounts-and-regions"></a>

You can execute AWS Systems Manager Automations across multiple AWS Regions and AWS accounts or AWS Organizational Units \(OUs\) from an Automation management account\. Executing Automations in multiple Regions and accounts or OUs reduces the time required to administer your AWS resources while enhancing the security of your computing environment\.

For example, you can centrally implement patching and security updates, remediate compliance drift on VPC configurations or Amazon S3 bucket policies, and manage resources, such as Amazon EC2 instances, at scale\. The following graphic shows an example of a user who is executing the AWS\-RestartEC2Instances document in multiple Regions and accounts from an Automation management account\. The Automation locates the instances by using the specified tags in the specified Regions and accounts\.

**Note**  
When you execute an Automation across multiple Regions and accounts, you target resources by using tags or the name of an AWS Resource Group\. The Automation fails to run on those resources that don't have the specified tag or that aren't included in the specified Resource Group\.

![\[Illustration showing Systems Manager Automation executing in multiple Regions and multiple accounts.\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/automation-multi-region-and-multi-account.png)

**Important**  
Your account is charged for executing Automations in multiple Regions and accounts\. Multi\-Region and account step executions are considered *special steps*\. There is no step limit for special steps, but your account is charged for each step processed by Systems Manager\. For more information, see the [AWS Systems Manager Pricing](https://aws.amazon.com/systems-manager/pricing/) page\.

**How It Works**  
Executing Automations across multiple Regions and accounts or OUs works as follows:

1. Verify that all resources on which you want to execute the Automation, in all Regions and accounts or OUs, use identical tags\. If they don't, you can add them to an AWS Resource Group and target that group\. For more information, see [What Is AWS Resource Groups?](https://docs.aws.amazon.com/ARG/latest/userguide/)

1. Log into the AWS Identity and Access Management \(IAM\) account that you want to configure as the Automation Master account\.

1. Use the procedure in this topic to create an IAM execution role called **AWS\-SystemsManager\-AutomationExecutionRole**\. This role gives the user permission to execute Automation workflows\.

1. Use the procedure in this topic to create a second IAM role called **AWS\-SystemsManager\-AutomationAdministrationRole**\. This role gives the user permission to execute Automation workflows in multiple AWS accounts and OUs\.

1. Choose the Automation document, Regions, and accounts or OUs where you want to execute the Automation workflow\.

1. Execute the Automation\.

1. Use the [GetAutomationExecution](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_GetAutomationExecution.html), [DescribeAutomationStepExecutions](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_DescribeAutomationStepExecutions.html), and [DescribeAutomationExecutions](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_DescribeAutomationExecutions.html) API actions from the AWS Systems Manager console or the AWS CLI to monitor workflow progress\.

## Setting Up Management Account Permissions for Multi\-Region and Multi\-Account Automation Execution<a name="systems-manager-automation-multiple-accounts-and-regions-permissions"></a>

Use the following procedure to create the required IAM roles for Systems Manager Automation multi\-Region and multi\-account execution by using AWS CloudFormation\. This procedure describes how to create the **AWS\-SystemsManager\-AutomationExecutionRole** role\. You must create this role in *every* account that you want to target to execute multi\-Region and multi\-account Automations\.

This procedure also describes how to create the **AWS\-SystemsManager\-AutomationAdministrationRole** role\. You only need to create this role in the Automation management account\.

**To create the required IAM roles for Multi\-Region and Multi\-Account Automation Executions by using AWS CloudFormation**

1. [Download](samples/AWS-SystemsManager-AutomationExecutionRole.zip) the AWS\-SystemsManager\-AutomationExecutionRole\.zip folder\. This folder includes the AWS\-SystemsManager\-AutomationExecutionRole\.json AWS CloudFormation template file\.

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Choose **Create Stack**\.

1. In the **Choose a template section**, choose **Upload a template to Amazon S3**\.

1. Choose **Browse**, and then choose the AWS\-SystemsManager\-AutomationExecutionRole\.json AWS CloudFormation template file\.

1. Choose **Next**\.

1. On the **Specify Details** page, in the **Stack Name** field, enter a name\. 

1. In the **Parameters** section, in the **MasterAccountID** field, enter the ID for the account that you want to use to execute multi\-Region and multi\-account Automations\.

1. Choose **Next**\.

1. On the **Options** page, enter values for any options you want to use\. Choose **Next**\.

1. On the **Review** page, scroll down and choose the **I acknowledge that AWS CloudFormation might create IAM resources** option\.

1. Choose **Create**\.

   AWS CloudFormation shows the **CREATE\_IN\_PROGRESS** status for approximately three minutes\. The status changes to **CREATE\_COMPLETE**\.

1. Repeat this procedure in *every* account that you want to target to execute multi\-Region and multi\-account Automations\.

1. [Download](samples/AWS-SystemManager-AutomationAdministrationRole.zip) the AWS\-SystemManager\-AutomationAdministrationRole\.zip folder and repeat this procedure for the AWS\-SystemManager\-AutomationAdministrationRole role\. You only need to create the AWS\-SystemManager\-AutomationAdministrationRole role in the Automation management account\.

## Execute an Automation in Multiple Regions and Accounts<a name="systems-manager-automation-multiple-accounts-and-regions-executing"></a>

Use the procedures in this section to execute an Automation in Multiple Regions and Accounts from the Automation management account\. Before you begin, make a note of the following information:
+ AWS account IDs or OUs where you want to execute the Automation\.
+ [AWS Systems Manager](https://docs.aws.amazon.com/general/latest/gr/rande.html) Regions where you want to execute the Automation\.
+ The tag key and the tag value, or the name of the Resource Group, where you want to execute the Automation\.

**To execute an Automation workflow in multiple Regions and accounts \(Console\)**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Automation**, and then choose **Execute automation**\.

1. In the **Automation document** list, choose the option beside a document name\. Use either the Search bar or the numbers to the right of the Search bar to view more Automation documents\. 
**Note**  
You can view information about a document by choosing the document name\.

1. In the **Document details** section, verify that **Document version** is set to the desired version you want to execute\. The system includes the following version options: 
   + **Default version at runtime**: Choose this option if the Automation document is updated periodically and a new default version is assigned\.
   + **Latest version at runtime**: Choose this option if the Automation document is updated periodically and you want to execute the version that was most recently updated\.
   + **Default**: Choose this option to execute the first, or default, version of the document\.

1. Choose **Next**\.

1. On the **Execute automation document** page, choose **Multi\-account and Region**\.

1. In the **Target accounts and Regions** section, use the **Accounts and organizational \(OUs\)** field to specify the different AWS accounts or AWS Organizational Units \(OUs\) where you want to execute the Automation\. Separate multiple accounts or OUs with a comma\. 

1. Use the **AWS Regions** list to choose one or more Regions where you want to execute the Automation\.

1. Use the **Multi\-Region and account rate control** options to restrict the Automation execution to a limited number of accounts running in a limited number of Regions\. These options don't restrict the number of AWS resources that can execute the Automations\. 

   1. In the **Location \(account\-Region pair\) concurrency** section, choose an option to restrict the number of Automation workflows that can execute in multiple accounts and Regions at the same time\. For example, if you choose to execute an Automation in 5 AWS accounts which are located in 4 AWS Regions, then Systems Manager executes Automations in a total of 20 account\-Region pairs\. You can use this option to specify an absolute number, such as 2, so that the Automation only runs in 2 account\-Region pairs at the same time\. Or you can specify a percentage of the account\-Region pairs that can execute at the same time\. For example, with 20 account\-Region pairs, if you specify 20%, then the Automation simultaneously executes in a maximum of 5 account\-Region pairs\. 
      + Choose **targets** to enter an absolute number of account\-Region pairs that can execute the Automation workflow simultaneously\.
      + Choose **percent** to enter a percentage of the total number of account\-Region pairs that can execute the Automation workflow simultaneously\.

   1. In the **Error threshold** section, choose an option:
      + Choose **errors** to enter an absolute number of errors allowed before Automation stops sending the workflow to other resources\.
      + Choose **percent** to enter a percentage of errors allowed before Automation stops sending the workflow to other resources\.

1. In the **Targets** section, choose how you want to target the AWS Resources where you want to run the Automation\. These options are required\.

   1. Use the **Parameter** list to choose a parameter\. The items in the **Parameter** list are determined by the parameters in the Automation document that you selected at the start of this procedure\. By choosing a parameter you define the type of resource on which the Automation workflow runs\. 

   1. Use the **Targets** list to choose how you want to target resources\. If you chose to target resources by using AWS Resource Groups, then choose the name of the group from the **Resource Group** list\. 

      If you chose to target resources by using tags, then enter the tag key and \(optionally\) the tag value in the fields provided\. Choose **Add**\.

1. In the **Input parameters** section, specify the required inputs\. Optionally, you can choose an IAM service role from the **AutomationAssumeRole** list\.
**Note**  
You may not need to choose some of the options in the **Input parameters** section\. This is because you targeted resources in multiple Regions and accounts by using tags or a Resource Group\. For example, if you chose the AWS\-RestartEC2Instance document, then you don't need to specify or choose instance IDs in the **Input parameters** section\. The Automation execution locates the instances to restart by using the tags you specified\. 

1. Use the options in the **Rate control** section to restrict the number of AWS resources that can execute the Automation within each account\-Region pair\. 

   In the **Concurrency** section, choose an option: 
   + Choose **targets** to enter an absolute number of targets that can execute the Automation workflow simultaneously\.
   + Choose **percentage** to enter a percentage of the target set that can execute the Automation workflow simultaneously\.

1. In the **Error threshold** section, choose an option:
   + Choose **errors** to enter an absolute number of errors allowed before Automation stops sending the workflow to other resources\.
   + Choose **percentage** to enter a percentage of errors allowed before Automation stops sending the workflow to other resources\.

1. Choose **Execute automation**\. 

**To execute an Automation workflow in multiple Regions and accounts \(AWS CLI\)**

1. [Download](https://aws.amazon.com/cli/) the latest version of the AWS CLI to your local machine\.

1. Open the AWS CLI and run the following command to specify your credentials and a Region\. You must either have administrator privileges, or you must have been granted the appropriate permission in AWS Identity and Access Management \(IAM\)\.

   ```
   aws configure
   ```

   The system prompts you to specify the following\.

   ```
   AWS Access Key ID [None]: key_name
   AWS Secret Access Key [None]: key_name
   Default region name [None]: region
   Default output format [None]: ENTER
   ```

1. Execute the following command to execute an Automation workflow in multiple Regions and accounts\.

   ```
   aws ssm start-automation-execution
   --document-name name_of_Automation_document
   --parameters AutomationAssumeRole=arn:aws:iam::Automation_management_account_ID:role/AWS-SystemsManager-AutomationAdministrationRole
   --target-parameter-name parameter_name (required)
   --targets Key=tag_key,Values=tag_value
   --target-locations Accounts=account_ID_1,account_ID_2,account_ID_3,Regions=Region_1,Region_2,ExecutionRoleName=AWS-SystemsManager-AutomationExecutionRole
   ```

   Here are a few examples:

   **Example 1**: This example restarts Amazon EC2 instances in the 1a2b3c4d5e6f7g8h and a1b2c3d4e5f6h7 accounts, which are located in the us\-east\-2 and us\-west\-1 Regions\. The instances must be tagged with Env\-PROD\.

   ```
   aws ssm start-automation-execution
   --document-name AWS-RestartEC2Instance
   --parameters AutomationAssumeRole=arn:aws:iam::123456789012:role/AWS-SystemsManager-AutomationAdministrationRole
   --target-parameter-name InstanceId
   --targets Key=tag:Env,Values=PROD
   --target-locations Accounts=1a2b3c4d5e6f7g8h,a1b2c3d4e5f6h7,Regions=us-east-2,us-west-1,ExecutionRoleName=AWS-SystemsManager-AutomationExecutionRole
   ```

   **Example 2**: This example restarts Amazon EC2 instances in the 1a2b3c4d5e6f7g8h and a1b2c3d4e5f6h7 accounts, which are located in the eu\-central\-1 Region\. The instances must be a member of the prod\-instances AWS Resource Group\.

   ```
   aws ssm start-automation-execution
   --document-name AWS-RestartEC2Instance
   --parameters AutomationAssumeRole=arn:aws:iam::123456789012:role/AWS-SystemsManager-AutomationAdministrationRole
   --target-parameter-name InstanceId
   --targets Key=ResourceGroup,Values=prod-instances
   --target-locations Accounts=1a2b3c4d5e6f7g8h,a1b2c3d4e5f6h7,Regions=eu-central-1,ExecutionRoleName=AWS-SystemsManager-AutomationExecutionRole
   ```

   **Example 3**: This example restarts Amazon EC2 instances in the ou\-1a2b3c\-4d5e6c AWS Organizational Unit which are located in the us\-west\-1 and us\-west\-2 Regions\. The instances must be a member of the WebServices AWS Resource Group\.

   ```
   aws ssm start-automation-execution
   --document-name AWS-RestartEC2Instance
   --parameters AutomationAssumeRole=arn:aws:iam::123456789012:role/AWS-SystemsManager-AutomationAdministrationRole
   --target-parameter-name InstanceId
   --targets Key=ResourceGroup,Values=WebServices
   --target-locations Accounts=ou-1a2b3c-4d5e6c,Regions=us-west-1,us-west-2
   ```

1. Execute the following command to view the workflow execution\.

   ```
   aws ssm describe-automation-executions --filters Key=ExecutionId,Values=ID
   ```

1. Execute the following command to view details about the execution progress\.

   ```
   aws ssm get-automation-execution --automation-execution-id ID
   ```
**Note**  
You can also monitor the status of the workflow in the console\. In the execution list, choose the execution you just ran and then choose the **Steps** tab\. This tab shows you the status of the workflow actions\.