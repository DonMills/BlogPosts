## Securing Remote Work with the Cloud:
### How AWS Workspaces can help keep your applications and data safe during these challenging times.
### By Don Mills, CISO

So I don't think it will surprise anyone if I make the statement that the average work day and environment has undertaken a complete shift over the last 6 or so months.  Employees that would have come to sit at a dedicated office space every day are now are having to work from a variety of places and situations that are not so... under your organization's control.

For a lot of companies, this has been a difficult and ongoing change -- even from purely procedural and cultural perspectives.  But for every business manager that worries how productivity will keep up in this new world, there equally IT operations and cyber security teams that are literally in a nightmare.

You see, most organizations have traditionally focused on a "fortification" strategy to protect their internal information technology assets.  There would be dedicated portions of the network where assets would be placed, and access to those assets was often enforced at a network level by firewalls and other dedicated devices.  Similarly, the employee would be issued a company configured and controlled desktop or laptop, which was often also protected by various enterprise anti-virus and endpoint protection tools.  The entire ecosystem was designed so that when Alicia was sitting at her desk, on her company provided laptop, she could then get to the company core applications...which often required a special client or software (thick client) to be installed locally on her work PC.

But now Alicia can't go to her office.  And even if she took her laptop home, it's still not on the company network.  To facilitate her getting to the core business applications she has to get to the company network through a Virtual Private Network (VPN) connection.  And that's how probably 98% of you reading this are doing it.

#### Operations through intermittent VPN connections

Unless you planned for this, and have lots of bandwidth (and robust scaleable VPN termination) I'm sure you've seen some interesting issues.  Not just the ones at first, where the load was more than anyone planned.  I mean the slow creep operational and security problems that have arisen. 

Like unless you prepared with SaaS or public virus updates, there's that virus database update that happens often when people connect to the VPN.  And unless you do automated updates direct from a public source, there's the matter of pushing patches and updates across the VPN connection.

Even then, although Alicia knows that her work laptop is for work, sometimes she uses it for other things.  And she happened to accidentally click on the wrong link during the weekend... and when she connected to the VPN Monday morning before the virus updates were pushed to her pc some interesting "software" made its way onto a server.

Here's another interesting one we've seen at SingleStone.  Since moving from on-premise Active Directory to all cloud (Azure AD) during the quarantine we still have some users that when they last logged into the directory with their laptops, it was on premise.  So they are operating with old credentials, but they can't change their passwords without someone __touching__ their laptop to change domain membership.

#### Three states of readiness

I consider there to be three broad categories of "remote work" readiness.
1. Zero trust - all of your apps are written to be accessed anywhere, via HTTPS.  You have publically accessible authentication and authorization methods, likely with hardware or software MFA.  Your workers can come on any device from anywhere.
2. High level VPN - you have the VPN infrastructure to support your entire workforce remotely.  You issue corporate compute devices, and have invested in SaaS or cloud based tools for anti-virus, endpoint protection, and device management/updates.  Email and office tools are SaaS or publically accessible, and the VPN exists to support access to legacy applications.
3. VPNish - you have a VPN infrastructure, but it was originally designed for disaster recovery.  Your operational and security tools are still mostly on-premise based, and access to pretty much everything is done over the VPN.




