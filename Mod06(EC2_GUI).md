<link rel="stylesheet" href="http://use.fontawesome.com/releases/v6.3.0/css/all.css">

# Lab 3: Introduction to Amazon EC2

<!-- Note to translators: This is based on SPL-200. Copy the translation from there. Do not re-translate the whole document. -->

## Lab overview and objectives

<img src="images/lab-scenario.jpeg" alt="architectural diagram" width="400">

&nbsp;

This lab provides you with a basic overview of launching, resizing, managing, and monitoring an Amazon EC2 instance.

**Amazon Elastic Compute Cloud (Amazon EC2)** is a web service that provides resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers.

Amazon EC2's simple web service interface allows you to obtain and configure capacity with minimal friction. It provides you with complete control of your computing resources and lets you run on Amazon's proven computing environment. Amazon EC2 reduces the time required to obtain and boot new server instances to minutes, allowing you to quickly scale capacity, both up and down, as your computing requirements change.

Amazon EC2 changes the economics of computing by allowing you to pay only for capacity that you actually use. Amazon EC2 provides developers the tools to build failure resilient applications and isolate themselves from common failure scenarios.



After completing this lab, you should be able to do the following:

  * Launch a web server with termination protection enabled
  * Monitor Your EC2 instance
  * Modify the security group that your web server is using to allow HTTP access
  * Resize your Amazon EC2 instance to scale
  * Explore EC2 limits
  * Test termination protection
  * Terminate your EC2 instance

&nbsp;

## Duration

This lab takes approximately **35 minutes** to complete.



## AWS service restrictions

In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that are described in this lab.



## Accessing the AWS Management Console

1. At the top of these instructions, choose <span id="ssb_voc_grey">Start Lab</span> to launch your lab.

    A Start Lab panel opens displaying the lab status.

    

2. Wait until you see the message "**Lab status: ready**", then choose the **X** to close the Start Lab panel.

    

3. At the top of these instructions, choose <span id="ssb_voc_grey">AWS</span>

    This will open the AWS Management Console in a new browser tab. The system will automatically log you in.

    **Tip**: If a new browser tab does not open, there will typically be a banner or icon at the top of your browser indicating that your browser is preventing the site from opening pop-up windows. Choose on the banner or icon and choose "Allow pop ups."

    

4. Arrange the AWS Management Console tab so that it displays along side these instructions. Ideally, you will be able to see both browser tabs at the same time, to make it easier to follow the lab steps.

&nbsp;
&nbsp;
## Task 1: Launch Your Amazon EC2 Instance

In this task, you will launch an Amazon EC2 instance with _termination protection_. Termination protection prevents you from accidentally terminating an EC2 instance. You will deploy your instance with a User Data script that will allow you to deploy a simple web server.



5. In the **AWS Management Console** choose <i class="fas fa-th"></i> **Services**, choose **Compute** and then choose **EC2**.

   **Note**: Verify that your EC2 console is currently managing resources in the **N. Virginia** (us-east-1) region.  You can verify this by looking at the drop down menu at the top of the screen, to the left of your username. If it does not already indicate N. Virginia, choose the N. Virginia region from the region menu before proceeding to the next step.

   

6. Choose the <span id="ssb_orange">Launch instance </span> menu and select **Launch instance**.

   

### Step 1: Name and tags

7. Give the instance the name `Web Server`.

   <i class="fas fa-info-circle"></i> The Name you give this instance will be stored as a tag. Tags enable you to categorize your AWS resources in different ways, for example, by purpose, owner, or environment. This is useful when you have many resources of the same type &mdash; you can quickly identify a specific resource based on the tags you have assigned to it. Each tag consists of a Key and a Value, both of which you define. You can define multiple tags to associate with the instance if you want to.

   In this case, the tag that will be created will consist of a *key* called `Name` with a *value* of `Web Server`

   

### Step 2: Application and OS Images (Amazon Machine Image)

8. In the list of available *Quick Start* AMIs, keep the default **Amazon Linux** AMI selected. 

   

9. Also keep the default **Amazon Linux 2023 AMI** selected.

   <i class="fas fa-info-circle"></i> An **Amazon Machine Image (AMI)** provides the information required to launch an instance, which is a virtual server in the cloud. An AMI includes:

   - A template for the root volume for the instance (for example, an operating system or an application server with applications)
   - Launch permissions that control which AWS accounts can use the AMI to launch instances
   - A block device mapping that specifies the volumes to attach to the instance when it is launched

   The **Quick Start** list contains the most commonly-used AMIs. You can also create your own AMI or select an AMI from the AWS Marketplace, an online store where you can sell or buy software that runs on AWS.

   

### Step 3: Instance type

10. In the *Instance type* panel, keep the default **t2.micro** selected.

    <i class="fas fa-info-circle"></i> Amazon EC2 provides a wide selection of _instance types_ optimized to fit different use cases. Instance types comprise varying combinations of CPU, memory, storage, and networking capacity and give you the flexibility to choose the appropriate mix of resources for your applications. Each instance type includes one or more _instance sizes_, allowing you to scale your resources to the requirements of your target workload.

    The t2.micro instance type has 1 virtual CPU and 1 GiB of memory. 

    **Note**: You may be restricted from using other instance types in this lab.

&nbsp;
### Step 4: Key pair (login)

11. For **Key pair name - *required***, choose **vockey**. 

    <i class="fas fa-info-circle"></i> Amazon EC2 uses public–key cryptography to encrypt and decrypt login information. To ensure you will be able to log in to the guest OS of the instance you create, you identify an existing key pair or create a new key pair when launching the instance. Amazon EC2 then installs the key on the guest OS when the instance is launched.  That way, when you attempt to login to the instance and you provide the private key, you will be authorized to connect to the instance.

    **Note**: In this lab you will not actually use the key pair you have specified to log into your instance.



### Step 5: Network settings

12. Next to Network settings, choose **Edit**.

    

13. For **VPC**, select **Lab VPC**.

    The Lab VPC was created using an AWS CloudFormation template during the setup process of your lab. This VPC includes two public subnets in two different Availability Zones.

    <i class="fas fa-info-circle"></i> **Note**: Keep the default subnet. This is the subnet in which the instance will run. Notice also that by default, the instance will be assigned a public IP address.

    

14. Under **Firewall (security groups)**, choose <i class="far fa-dot-circle"></i> **Create security group** and configure:

    - **Security group name:** `Web Server security group`

    - **Description:** `Security group for my web server`

      <i class="fas fa-info-circle"></i> A _security group_ acts as a virtual firewall that controls the traffic for one or more instances. When you launch an instance, you associate one or more security groups with the instance. You add _rules_ to each security group that allow traffic to or from its associated instances. You can modify the rules for a security group at any time; the new rules are automatically applied to all instances that are associated with the security group.

    - Under **Inbound security group rules**, notice that one rule exists. **Remove** this rule.



### Step 6: Configure storage

15. In the *Configure storage* section, keep the default settings.

    <i class="fas fa-info-circle"></i> Amazon EC2 stores data on a network-attached virtual disk called *Elastic Block Store*.

    You will launch the Amazon EC2 instance using a default 8 GiB disk volume. This will be your root volume (also known as a 'boot' volume).



### Step 7: Advanced details

16. Expand <i class="fas fa-caret-right"></i> **Advanced details**.

    

17. For **Termination protection**, select **Enable**.

    <i class="fas fa-info-circle"></i>  When an Amazon EC2 instance is no longer required, it can be *terminated*, which means that the instance is deleted and its resources are released. A terminated instance cannot be accessed again and the data that was on it cannot be recovered. If you want to prevent the instance from being accidentally terminated, you can enable *termination protection* for the instance, which prevents it from being terminated as long as this setting remains enabled.

    

18. Scroll to the bottom of the page and then copy and paste the code shown below into the **User data** box:

    ```bash
    #!/bin/bash
    dnf install -y httpd
    systemctl enable httpd
    systemctl start httpd
    echo '<html><h1>Hello From Your Web Server!</h1></html>' > /var/www/html/index.html
    ```

    <i class="fas fa-info-circle"></i>  When you launch an instance, you can pass _user data_ to the instance that can be used to perform automated installation and configuration tasks after the instance starts.

    Your instance is running Amazon Linux 2023. The _shell script_ you have specified will run as the *root* guest OS user when the instance starts. The script will:

    - Install an Apache web server (httpd)
    - Configure the web server to automatically start on boot
    - Run the Web server once it has finished installing
    - Create a simple web page



### Step 8: Launch the instance

19. At the bottom of the **Summary** panel on the right side of the screen choose <span id="ssb_orange">Launch instance</span>

    You will see a Success message.



20. Choose <span id="ssb_orange">View all instances</span>

    - In the Instances list, select <i class="fas fa-square" style="color:blue"></i> **Web Server**. 

    - Review the information displayed in the **Details** tab. It includes information about the instance type, security settings and network settings.

      The instance is assigned a _Public IPv4 DNS_ that you can use to contact the instance from the Internet.

      <i class="fas fa-comment"></i> To view more information, drag the window divider upwards.

      At first, the instance will appear in a _Pending_ state, which means it is being launched. It will then change to *Initializing*, and finally to _Running_.

    

21. Wait for your instance to display the following:
    - **Instance State:** <span style="color:green"><i class="fas fa-circle"></i></span> *Running*
    - **Status Checks:**  <span style="color:green"><i class="fas fa-check-circle"></i></span> *2/2 checks passed*



<span style="color:blue"><i class="far fa-thumbs-up"></i></span> **Congratulations!** You have successfully launched your first Amazon EC2 instance.



## Task 2: Monitor Your Instance

Monitoring is an important part of maintaining the reliability, availability, and performance of your Amazon Elastic Compute Cloud (Amazon EC2) instances and your AWS solutions.



22. Choose the **Status checks** tab.

    <i class="fas fa-info-circle"></i> With instance status monitoring, you can quickly determine whether Amazon EC2 has detected any problems that might prevent your instances from running applications. Amazon EC2 performs automated checks on every running EC2 instance to identify hardware and software issues.

    Notice that both the **System reachability** and **Instance reachability** checks have passed.

    

23. Choose the **Monitoring** tab.

    This tab displays Amazon CloudWatch metrics for your instance. Currently, there are not many metrics to display because the instance was recently launched.

    You can choose the three dots icon in any graph and select **Enlarge** to see an expanded view of the chosen metric.

    <i class="fas fa-info-circle"></i> Amazon EC2 sends metrics to Amazon CloudWatch for your EC2 instances. Basic (five-minute) monitoring is enabled by default. You can also enable detailed (one-minute) monitoring.

    

24. In the <span id="ssb_grey_square">Actions <i class="fas fa-caret-down"></i></span> menu towards the top of the console, select **Monitor and troubleshoot** <i class="fas fa-caret-right"></i> **Get system log**.

    The System Log displays the console output of the instance, which is a valuable tool for problem diagnosis. It is especially useful for troubleshooting kernel problems and service configuration issues that could cause an instance to terminate or become unreachable before its SSH daemon can be started. If you do not see a system log, wait a few minutes and then try again.

    

25. Scroll through the output and note that the HTTP package was installed from the **user data** that you added when you created the instance.

    <img src="images/Console-output.png" alt="Console-Output">

26. Choose **Cancel**.

27. Ensure **Web Server** is still selected. Then, in the <span id="ssb_grey_square">Actions <i class="fas fa-caret-down"></i></span> menu, select **Monitor and troubleshoot** <i class="fas fa-caret-right"></i> **Get instance screenshot**.

    This shows you what your Amazon EC2 instance console would look like if a screen were attached to it.

    <img src="images/Screen-shot.png" alt="Screen-shot">

    

    <i class="fas fa-info-circle"></i> If you are unable to reach your instance via SSH or RDP, you can capture a screenshot of your instance and view it as an image. This provides visibility as to the status of the instance, and allows for quicker troubleshooting.

    

28. Choose **Cancel**.

    <span style="color:blue"><i class="far fa-thumbs-up"></i></span> **Congratulations!** You have explored several ways to monitor your instance.

&nbsp;
&nbsp;
## Task 3: Update Your Security Group and Access the Web Server

When you launched the EC2 instance, you provided a script that installed a web server and created a simple web page. In this task, you will access content from the web server.




29. Ensure **Web Server** is still selected.  Choose the **Details** tab.

    

30. Copy the **Public IPv4 address** of your instance to your clipboard.

    

31. Open a new tab in your web browser, paste the IP address you just copied, then press **Enter**.

    **Question:** Are you able to access your web server? Why not?

    You are **not** currently able to access your web server because the _security group_ is not permitting inbound traffic on port 80, which is used for HTTP web requests. This is a demonstration of using a security group as a firewall to restrict the network traffic that is allowed in and out of an instance.

    To correct this, you will now update the security group to permit web traffic on port 80.

    

32. Keep the browser tab open, but return to the **EC2 Console** tab.

    

33. In the left navigation pane, choose **Security Groups**.

    

34. Select <i class="fas fa-square" style="color:blue"></i> **Web Server security group**.

    

35. Choose the **Inbound rules** tab.

    The security group currently has no inbound rules.

    

36. Choose <span id="ssb_grey_square">Edit inbound rules</span>, select <span id="ssb_grey_square">Add rule</span> and then configure:

    * **Type:** _HTTP_

    * **Source:** _Anywhere-IPv4_

    * Choose <span id="ssb_orange">Save rules</span>

      

37. Return to the web server tab that you previously opened and refresh <i class="fas fa-sync"></i> the page.

    You should see the message _Hello From Your Web Server!_

    

    <span style="color:blue"><i class="far fa-thumbs-up"></i></span> **Congratulations!** You have successfully modified your security group to permit HTTP traffic into your Amazon EC2 Instance.

&nbsp;
&nbsp;
## Task 4: Resize Your Instance: Instance Type and EBS Volume

As your needs change, you might find that your instance is over-utilized (too small) or under-utilized (too large). If so, you can change the _instance type_. For example, if a _t2.micro_ instance is too small for its workload, you can change it to an _m5.medium_ instance. Similarly, you can change the size of a disk.




### Stop Your Instance

Before you can resize an instance, you must _stop_ it.

<i class="fas fa-info-circle"></i> When you stop an instance, it is shut down. There is no runtime charge for a stopped EC2 instance, but the storage charge for attached Amazon EBS volumes remains.

38. On the **EC2 Management Console**, in the left navigation pane, choose **Instances**.

    <i class="fas fa-square" style="color:blue"></i> **Web Server** should already be selected.

    

39. In the <span id="ssb_grey_square">Instance State <i class="fas fa-caret-down"></i></span> menu, select **Stop instance**.

    

40. Choose <span id="ssb_orange">Stop</span>

    Your instance will perform a normal shutdown and then will stop running.

    

41. Wait for the **Instance state** to display: <span style="color:red"><i class="fas fa-circle"></i></span> *Stopped*.

    

### Change The Instance Type

42. In the <span id="ssb_grey_square">Actions <i class="fas fa-caret-down"></i></span> menu, select **Instance settings** <i class="fas fa-caret-right"></i> **Change instance type**, then configure:

    * **Instance Type:** *t2.small*
    
    * Choose <span id="ssb_orange">Apply</span>
    
      When the instance is started again it will run as a _t2.small_, which has twice as much memory as a _t2.micro_ instance. **NOTE**: You may be restricted from using other instance types in this lab.
    
    

### Resize the EBS Volume

43. With the Web Server instance still selected, choose the **Storage** tab, select the name of the Volume ID, then select the checkbox next to the volume that displays.

    

44. In the <span id="ssb_grey">Actions <i class="fas fa-caret-down"></i></span> menu, select **Modify volume**.

    The disk volume currently has a size of 8 GiB. You will now increase the size of this disk.

    

45. Change the size to: `10` **NOTE**: You may be restricted from creating large Amazon EBS volumes in this lab.

    

46. Choose <span id="ssb_grey">Modify</span>

    

47. Choose <span id="ssb_blue">Modify</span> again to confirm and increase the size of the volume.

    

### Start the Resized Instance

You will now start the instance again, which will now have more memory and more disk space.

49. In left navigation pane, choose **Instances**.

    

50. Select the **Web Server** instance.

    

51. In the <span id="ssb_grey_square">Instance state <i class="fas fa-caret-down"></i></span> menu, select **Start instance**.

    <span style="color:blue"><i class="far fa-thumbs-up"></i></span> **Congratulations!** You have successfully resized your Amazon EC2 Instance. In this task you changed your instance type from _t2.micro_ to _t2.small_. You also modified your root disk volume from 8 GiB to 10 GiB.

    


## Task 5: Explore EC2 Limits

Amazon EC2 provides different resources that you can use. These resources include images, instances, volumes, and snapshots. When you create an AWS account, there are default limits on these resources on a per-region basis.



52. In the AWS Management Console, in the search box next to <i class="fas fa-th"></i> **Services**, search for and choose `Service Quotas`

    

53. Choose **AWS services** from the navigation menu and then in the AWS services *Find services* search bar, search for `ec2` and choose **Amazon Elastic Compute Cloud (Amazon EC2)**.

    

54. In the *Find quotas* search bar, search for `running on-demand`, but do not make a selection. Instead, observe the filtered list of service quotas that match the criteria.

    Notice that there are limits on the number and types of instances that can run in a region. For example, there is a limit on the number of *Running On-Demand Standard...* instances that you can launch in this region. When launching instances, the request must not cause your usage to exceed the instance limits currently defined in that region.

    You can request an increase for many of these limits.

    


## Task 6: Test Termination Protection

You can delete your instance when you no longer need it. This is referred to as _terminating_ your instance. You cannot connect to or restart an instance after it has been terminated. In this task, you will learn how to use _termination protection_.



55. In the AWS Management Console, in the search box next to <i class="fas fa-th"></i> **Services**, search for and choose `EC2` to return to the EC2 console.

    

56. In left navigation pane, choose **Instances**.

    

57. Select the **Web Server** instance and in the <span id="ssb_grey_square">Instance state <i class="fas fa-caret-down"></i></span> menu, select **Terminate instance**.

    

58. Then choose <span id="ssb_orange">Terminate</span>

    Note that there is a message that says: *Failed to terminate the instance i-1234567xxx. The instance 'i-1234567xxx' may not be terminated. Modify its 'disableApiTermination' instance attribute and try again.*

    This is a safeguard to prevent the accidental termination of an instance. If you really want to terminate the instance, you will need to disable the termination protection.

    

59. In the <span id="ssb_grey_square">Actions <i class="fas fa-caret-down"></i></span> menu, select **Instance settings** <i class="fas fa-caret-right"></i> **Change termination protection**.

    

60. Remove the check next to <i class="far fa-square"></i> **Enable**.

    

61. Choose <span id="ssb_orange">Save</span>

    You can now terminate the instance.

    

62. Select the **Web Server** instance again and in the <span id="ssb_grey_square">Instance state <i class="fas fa-caret-down"></i></span> menu, select **Terminate instance**.

    

63. Choose <span id="ssb_orange">Terminate</span>

    <span style="color:blue"><i class="far fa-thumbs-up"></i></span> **Congratulations!** You have successfully tested termination protection and terminated your instance.



## Lab Complete

<i class="icon-flag-checkered"></i> Congratulations! You have completed the lab.



64. Choose <span id="ssb_voc_grey">End Lab</span> at the top of this page and then choose <span id="ssb_blue">Yes</span> to confirm that you want to end the lab.  

    An End Lab panel will appear, indicating that "You may close this message box now."

    

65. Choose the **X** in the top right corner to close the panel.

    


## Additional Resources

* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html" target="_blank">Launch Your Instance</a>
* <a href="https://aws.amazon.com/ec2/instance-types" target="_blank">Amazon EC2 Instance Types</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html" target="_blank">Amazon Machine Images (AMI)</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html" target="_blank">Amazon EC2 - User Data and Shell Scripts</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/RootDeviceStorage.html" target="_blank">Amazon EC2 Root Device Volume</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html" target="_blank">Tagging Your Amazon EC2 Resources</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html" target="_blank">Security Groups</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html" target="_blank">Amazon EC2 Key Pairs</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-system-instance-status-check.html?icmpid=docs_ec2_console" target="_blank">Status Checks for Your Instances</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-console.html" target="_blank">Getting Console Output and Rebooting Instances</a>
* <a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ec2-metricscollected.html" target="_blank">Amazon EC2 Metrics and Dimensions</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-resize.html" target="_blank">Resizing Your Instance</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html" target="_blank">Stop and Start Your Instance</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html" target="_blank">Amazon EC2 Service Limits</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html" target="_blank">Terminate Your Instance</a>
* <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html" target="_blank">Termination Protection for an Instance</a>



© 2023, Amazon Web Services, Inc. and its affiliates. All rights reserved. This work may not be reproduced or redistributed, in whole or in part, without prior written permission from Amazon Web Services, Inc. Commercial copying, lending, or selling is prohibited.
