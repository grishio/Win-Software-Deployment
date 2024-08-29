# Grish Win Software Deployment (GWSD)

## Description
Grish Win Software Deployment (GWSD) is an open source tool for Windows that aims to replace something like SCCM's Software Center. It minimizes the management footprint by not requiring any server-sided components. This will not fit all organizational requirements that depend on things such as reporting, but is fine where there are basic requirement. WSD is a client-sided utility that lists available software for the user by relying on SMB file shares. Security, distribution, and availability of software for the end-user/machine is accomplished by enabling features such as access-based enumeration for SMB file shares. Built-in windows utilities such as Robocopy, PowerShell, and Group Policies aid with downloading and deploying packages.

**The client component will have two components to it:**
First, the client interface itself that the user interacts with. The user will be able to view what software they have available for installation and will be able to perform actions within the user-scope (non-admin). The second is the windows service. The windows service will aid in assisting with executing installation scripts as administrator when required, and dealing with deployments pushed within the computer scope.

**Requirements**
1. SMB File Share with access-based enumeration enabled.
2. Folder structure as defined in the architecture section below.
3. File Server
4. Domain joined users and computers

## Architecture
**Deployment and configuration**
1. Via Group Policy, define and deploy this key, which will define what servers GWSD will query:  
    **Key:** HKEY_LOCAL_MACHINE\SOFTWARE\GWSD\  
    **String Example 1:** "Office Synology":"\\Synology\Software"  
    **String Example 2:** "Arizona DC":"\\arizona\Software"
2. Create the shares with a default of no permissions for any software versions itself. Each new software added should have it's own security group. Essentially, you are disabling inheritance beyond a certain point.
3. Deploy GWSD to all machines via Group Policy (you can define your scope). A PowerShell Script will be available to automate this.

**Folder Structure**  
-- Base of the SMB Share  
&nbsp;-- Software Vendor  
&nbsp;&nbsp;-- Software name  
&nbsp;&nbsp;&nbsp;-- Version  
&nbsp;&nbsp;&nbsp;&nbsp;-- *The actual installation files*

**GWSD Operation**
1. User requests software
2. IT adds the user or machine (depending on the app scope) to an AD Security Group that controls access to the software folder.
3. As the shares are access-based enumerated, the new software becomes "available". The GWSD client sees the folder now, it able to index it, and adds it to the users available software list.