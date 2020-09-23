## Securing Remote Work with the Cloud:
### Five ways AWS Workspaces can help keep your applications and data safe during these challenging times.
### By Don Mills, CISO

So I don't think it will surprise anyone if I make the statement that the average work day and environment has undertaken a complete shift over the last 6 or so months.  Employees that would have come to sit at a dedicated office space every day are now are having to work from a variety of places and situations that are not so... under your organization's control.

For a lot of companies, this has been a difficult and ongoing change -- even from purely procedural and cultural perspectives.  But for every business manager that worries how productivity will keep up in this new world, there are equally IT operations and cyber security teams that are under pressure.

You see, most organizations have traditionally focused on a "fortification" strategy to protect their internal information technology assets.  There would be dedicated portions of the network where assets would be placed, and access to those assets was often enforced at a network level by firewalls and other dedicated devices.  Similarly, the employee would be issued a company configured and controlled desktop or laptop, which was often also protected by various enterprise anti-virus and endpoint protection tools.  The entire ecosystem was designed so that when Alicia was sitting at her desk, on her company provided laptop, she could then get to the company core applications...which often required a special client or software (thick client) to be installed locally on her work PC.

But now Alicia can't go to her office.  And even if she took her laptop home, it's still not on the company network.  To facilitate her getting to the core business applications she has to get to the company network through a Virtual Private Network (VPN) connection.  And that's how probably 98% of you reading this are doing it.

#### Operations through intermittent VPN connections

Unless you planned for this, and have lots of bandwidth (and robust, scalable VPN termination) I'm sure you've seen some interesting issues.  Not just the ones at first - where the load was more than anyone planned.  I mean the slow, creeping operational and security problems that have arisen. 

Like unless you prepared with SaaS or public virus updates, there's that virus database update that happens often when people connect to the VPN.  And unless you do automated updates direct from a public source, there's the matter of pushing patches and updates across the VPN connection.

Even then, although Alicia knows that her work laptop is for work, sometimes she uses it for other things.  And she happened to accidentally click on the wrong link during the weekend... and when she connected to the VPN Monday morning before the virus updates were pushed to her pc some interesting "software" made its way onto a server.

#### Three states of readiness

I consider there to be three broad categories of "remote work" readiness.
1. Zero trust - all of your apps are written to be accessed anywhere, via HTTPS.  You have publically accessible authentication and authorization methods, likely with hardware or software MFA.  Your workers can come on any device from anywhere.
2. Hybrid VPN - you have the VPN infrastructure to support your entire workforce remotely.  You issue corporate compute devices, and have invested in SaaS or cloud based tools for anti-virus, endpoint protection, and device management/updates.  Email and office tools are SaaS or publically accessible, and the VPN exists to support access to legacy applications.
3. All-in VPN - you have a VPN infrastructure, but it was likely originally designed for disaster recovery.  You issue corporate compute devices, but your operational and security tools are still mostly on-premise based, and access to pretty much everything is done over the VPN.

Unless you are one of the mythical and fortunate few that can honestly claim to be in the first category, the move to all remote work has likely caused you at least some issues, even if only financial impact for hardware and software costs.

#### Desktops as a Service?

[AWS Workspaces](https://aws.amazon.com/workspaces/) are a cloud spin on an old idea - Virtual Desktops. Wait, no - don't stop reading. Give me a minute here.  

We all know that VDI (virtual desktop initiatives) have long been overly complex and expensive.  While some places still use them, they've fallen out of favor over the years.  The cost of the dedicated storage alone can be prohibitive.

But you know who has tons of storage and compute power ready?  AWS! And so they offer AWS Workspaces, which they refer to as "Desktops as a Service".

AWS Workspaces, if you are at all familiar with Amazon Web Services, are the desktop versions of the classic EC2 infrastructure instances.  They can live inside your AWS Virtual Private Cloud (VPC), and they are provisioned and managed centrally through a dedicated set of tools within the AWS Console.  Users connect to these instances via a dedicated AWS client that uses the PCoIP (PC over IP protocol) to provide a seemless desktop experience.

#### Five ways AWS Workspaces can help you today

1. __Financial__:  I put this first as it is always a huge incentive to be able to provide services at lower cost.  AWS Workspaces can help in multiple ways here, but a few to consider are:  
  * Cheaper end devices - AWS Workspaces keep all the compute and storage in the cloud, and thus your users need very minimal in the way of local equipment.  The Workspaces client runs on most operating systems, as well as Android and IOS.  So everything from tablets to Chromebooks to lower end Windows laptops are perfectly fine as a display terminal for the remote workspace  
  * Less dedicated VPN infrastructure - Since Workspaces use the AWS Cloud with it's huge amount of bandwith, you no longer need to funnel all your workers through on-premise VPN devices (which required at least two for redundancy, more licensing, more circuits...)  
    
2. __Provide secured work environment__:  Unless you have a very comprehensive security program, and have purchased some very expensive tools, you know as well as I that you can  configure a secure laptop and hand it to someone...but they still have physical access.  And to make it worse, a week or two after you hand it to them, it's probably out of date for patches and updates.  Now we discussed doing this over the VPN above, and if that works for you...great.  But for everyone else, Workspaces have some advantages.
  * Master or Golden Image capability - Since Workspaces are virtual machines, you can create a highly secured and customized "golden" or master image for your purposes, and use it to stamp out as many workspaces as you want.  
  * Updates - As regular updates occur, you can update the golden image instead of each individual workspace.  Then you can rebuild all existing workspaces with the new image for seamless updating.
  * Encrypted drives - You can use AWS's Key Management System (KMS) to encrypt the user volumes on the Workspace.
  * No physical access - This means no USB drives, or other external physical media to bring malicious code into your environment. And, even if you _don't_ encrypt your Workspace drives, a lost laptop will not put your data in danger.
  
3. __Increased resiliency__: By using the underlying toolsets that AWS provides, Workspaces can be made very resilient.  
  * Dedicated user drive - Workspaces provide a dedicated "User" data drive (such as a D: drive in Windows OS's) that is used to store all user created information. This drive is an Elastic Block Store (EBS) volume under the hood, and thus has all the resiliency of that service.  This separation of user data and operating system allows Workspaces to be rebuilt and updated without loss of user data.
  * Automated snapshots - All Workspace volumes have a snapshot taken every 12 hours.  This can be used to restore or rebuild any Workspace off of a snapshot.
  * Regional failover - An individual workspace can be made geographically resilient through DNS failover to a second workspace in another region.  By using the AWS WorkDocs feature, user data can be securely replicated between regional instances.
  
4. __Keep current security posture__: If you are currently dependent on the "Fortification" strategy of security, or you have specific security constraints around network level access to your business applications, Workspaces are an excellent choice.  The only requirement is the thought process of moving your secure network boundary to include the Workspaces.
  * Secure access to AWS hosted applications - If you already have invested in AWS to host your internal applications, then by using Workspaces you can give your users secure access to those applications across the AWS backbone, or even inside the same Virtual Private Cloud (VPC). 
  * Secure access to on-premise applications - Applications that reside on-premise can be reached securely by Workspaces either through much cheaper point-to-point (lan-to-lan) VPN from AWS to your datacenter, or through dedicated Direct Connect circuits.
  * Concerns about access to Workspaces - If the fact that Workspaces are cloud based is an issue, there are multiple ways to mitigate concerns.  IP address connection filtering, requiring certificates to connect, and specific lockdowns to specific client machines are all features.
 
5. __Utilize current operations and security tools__:  Workspaces are provisioned per user using Active Directory.  This allows you to use your current AD investment to provision and authenticate your users.  Your current AD login and password will be what you use to gain access to your Workspace.
  * Managed with on-premise tools - You can use the Direct Connect or point-to-point VPN connection mentioned above to manage Workspaces with your current on-premise tools __today__.
  * Just like your own devices, but in the cloud - Since you are providing a customized, secured, and configured work environment to your end users, the management of the workspaces is just like your traditional on-premise desktops and laptops (without hardware failures).  All your familiar tools can be used for monitoring, management, and reporting.
  * Bring Your Own License (BYOL) - If you have significant investment in Windows licensing, Workspaces can also use your enterprise licenses for cheaper costs.
  
#### Wrapping it up
  
Sounds pretty good, doesn't it?  I think Workspaces are an underutilized AWS service that may well be coming into its own due to our current situation.

If you think you might be interested in putting the power of AWS Workspaces to work for you then contact SingleStone Consulting today.  We can help you with any part of your cloud journey, and we can help you do it correctly and securely the first time.  Or, if you are ready to truly attack "zero trust", our Security and AppDev teams can make it manageable.


  
  


