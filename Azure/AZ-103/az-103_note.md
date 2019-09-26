---
title: AZ-103 note
author: ichimei0125
---
### Microsoft Documents
* [storage](https://docs.microsoft.com/en-us/azure/storage/)
  * [cdn](https://docs.microsoft.com/en-us/azure/cdn/)
* [vm](https://docs.microsoft.com/en-us/azure/virtual-machines/)
* [vnet](https://docs.microsoft.com/en-us/azure/#Headingvirtual-network/)
  * [dns](https://docs.microsoft.com/en-us/azure/dns/)
  * [vpn](https://docs.microsoft.com/en-us/azure/vpn-gateway/)
  * [load balancer](https://docs.microsoft.com/en-us/azure/load-balancer/)
  * [Network Watcher](https://docs.microsoft.com/en-us/azure/network-watcher/)
* [AD](https://docs.microsoft.com/en-us/azure/active-directory/)
  * [MFA](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-mfa-howitworks)
  * [conditional access](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/)
  * [built-in roles](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles)
* [policy](https://docs.microsoft.com/en-us/azure/governance/policy/)
* [backup](https://docs.microsoft.com/en-us/azure/backup/)

***

## Manage Azure subscriptions and resources (15-20%)

### Manage [Azure subscriptions](https://docs.microsoft.com/en-us/azure/billing/)

> * A tenant is a dedicated, isolated instance of the Azure Active Directory service, owned and managed by an organization.  
> * The email address you use to sign in to Azure can be associated with more than one tenant  
> * choose a different tenant in the Switch directory section  
> * Azure AD tenants and subscriptions have a many-to-one trust relationship:  
> * A tenant can be associated with multiple Azure subscriptions, but every subscription is associated with only one tenant.

* assign administrator permissions
* configure cost center quotas and tagging
  * [cost analysis](https://docs.microsoft.com/en-us/azure/cost-management/quick-acm-cost-analysis)  
  * tagging, resource group, subscription -- three ways to do cost management
  * Cost analysis, Invoices will show details, not overview.
* configure Azure subscription policies at Azure subscription level  

### Analyze resource utilization and consumption

* configure diagnostic settings on resources
  * VM need install agent
  * Diagnostics settings in the Subscriptions tabs does not exist.
* create baseline for resources
  1. resource group - deployments -> template
  1. resource / resource group -> automation scripts
  1. All services -> templates
  1. powershell script (cannot generate, manual)
* create and rest alerts
  * [budget alert](https://docs.microsoft.com/en-us/azure/cost-management/tutorial-acm-create-budgets)
  * resource
  * alert condition
  * action -> action group
* analyze [alerts](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-overview) across subscription
* analyze [metrics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-supported) across subscription
* create [action groups](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups)
  * notification
    1. email, sms, phone call
    1. azure function
    1. logic app
    1. webhook
    1. ITSM (IT Service Management Connector Solution )
    1. Automation Runbook
* monitor for unused resources
  * const analysis
  * invoices
* monitor spend
* report on spend
* utilize [Log Search query](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-queries) functions
* view alerts in Log Analytics
  * need create a workspace
  * alert

### Manage [resource groups](https://docs.microsoft.com/en-us/azure/azure-resource-manager/manage-resource-groups-portal?WT.mc_id=AzPortal_ResourceGroups_CmdBar_DocLink)  

* use Azure policies for resource groups
* configure [resource locks](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources)  
  * support subscription level
  ```json
  "type": "Microsoft.Web/sites/providers/locks"
  ```
  * CanNotDelete  
  * ReadOnly  
    > Applying Read-only can lead to unexpected results because some operations that seem like read operations actually require additional actions.  
    > For example, placing a Read-only lock on a storage account prevents all users from listing the keys. The list keys operation is handled through a POST request because the returned keys are available for write operations.
* configure resource [policies](https://docs.microsoft.com/en-us/azure/governance/policy/overview)
  * create -> policy-assign(__Don't forget assignment__)
  * effect field
    * disable
    * deny
    * audit
    * append (has detail field)
    * denyifnotexist (has detail field)
    * auditifnotexist (has detail field)
* identify auditing requirements
* implement and set tagging on resource groups
  * Tags are name/value pairs of text data
  * Not all resources support tags
  * Tags applied at a resource group level are __not__ propagated to resources within the resource group
* move resources across resource groups
  * source and target are all be locked
  * source will not stop
  * make sure register provider
  * promission  
    * **source** 
    Microsoft.Resources/subscriptions/resourceGroups/moveResources/action。
    * **target**  
    Microsoft.Resources/subscriptions/resourceGroups/write。
  * move between subscription can only accept one resource group at same time.
* remove resource groups

### Managed role based access control ([RBAC](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview))

* create a custom role
  * need Azure AD premium P1 / P2
  ```powershell
  $role = Get-AzRoleDefinition "Virtual Machine Contributor"
  # change something
  New-AzRoleDefinition -Role $role # create new
  Set-AzRoleDefinition # update existed
  ```
* configure access to Azure resources by assigning roles
  * Owner:  
  create permission to other users, full permission
  * Contributor:  
  full managed resources
  * Reader:  
  read only
* configure management access to Azure  
* troubleshoot RBAC  
  * User must have ```Microsoft.Authorization/roleDefinition/write```
* implement RBAC policies  
* assign RBAC Roles

***

## Implement and manage storage (15-20%)

|StorageV1|Blob Storage|Block Blob Storage|
|:-|:-|:-|
|does not support **hot/cool tier** access <br/> does not support the **Zone-redundant** option|standard, Only blob (blocks/append), no ZRS|Premium, only LRS|

Create and configure [storage accounts](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview)

* configure network access to the storage account
* create and configure storage account
  * cannot change performance setting after storage account created.
* generate shared access signature
* install and use Azure Storage Explorer
* manage access keys
* monitor activity log by using Log Analytics
  * log analytics's data source:
     1. vm
     1. storage account
     1. azure activity log
     1. azure resource
* implement Azure storage replication
  * LRS、ZRS、GRS、RA-GRS、GZRS、RA-GZRS

Import and export data to Azure

> dataset CSV: a list of directories and/or a list of files to be copied to target drives.  
```csv
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

![data transfer](https://docs.microsoft.com/en-us/azure/storage/common/media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

* create export from Azure job, create import into Azure job
  * [Azure Import/Export Job](https://docs.microsoft.com/en-us/azure/storage/common/storage-import-export-service)
  * Blob/File Import
     1. prepare drives
     1. create import job(upload generated file from step.1)
     1. ship drives
     1. update job(track information)
     1. confirm
  * Blob Export
     1. create export job
     1. ship disk
     1. update job(tracking information)
     1. receive disks
* Use Azure Data Box
  * offline
    * Data Box Disk: < 40TB
    * Data Box: 40TB ~ 500TB
    * Data Box Heavy: > 500TB ~ 1PB
  * online
    * Data Box Gateway: virtual device with storage
    * Data Box Edge
* configure and use Azure blob storage
* configure Azure content delivery network ([CDN](https://docs.microsoft.com/en-us/azure/cdn/)) endpoints
  * Add a custom domain
     1. Create a CNAME DNS record.
     1. Associate the custom domain with your CDN endpoint.  
     (At blob Setting, not CDN Setting)
     1. Verify the custom domain.
  * [compatation of different CDN types](https://docs.microsoft.com/en-us/azure/cdn/cdn-features)

Configure Azure files

* create Azure file share
* create [Azure File Sync](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-planning) service
  1. Prepare your environment  
     Windows Server only
     * Windows Sever 2019
     * Windows Sever 2016
     * Windows Sever 2012 R2
  1. Deploy the service
     * create storage account -> file share
  1. Install the agent  
    On Windows PC
     1. powershell script, run Azure File Sync evaluation tool
     1. install agent -> server registration
  1. Register Windows Server
  1. Create a sync group  
     Storage Sync Service -> Sync group
      > possible to join different AD memberships, even not domain-joined  
     * cloud endpoint -> Storage account(file share)
     * server endpoint -> Windows Server
  1. Add a server endpoint
     * registed server - path
     * [cloud tiering](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-cloud-tiering)  
    The cloud tiering feature is used to ensure volumes have a percentage of free space when you use the Azure File Sync service. 
* create Azure sync group
* troubleshoot Azure File Sync

Implement [Azure backup](https://docs.microsoft.com/en-us/azure/backup/)

* [configure and review backup reports](https://docs.microsoft.com/en-us/azure/backup/backup-azure-configure-reports)
  * prerequire
    1. storage account -> storage report-related data  
      archive to a storage account
    1. Power BI -> view, customize, create report
    1. Microsoft.insights -> enable at ```Subscription > Resource providers```
  >After you configure reports by saving the storage account, wait for 24 hours for the initial data push to finish. Import the Azure Backup App in Power BI only after that time.
* perform backup operation
  * resource and recover services **must in same region**  
  * First step: **Create Recover services vault**
  * backable types
     1. [vm backup](https://docs.microsoft.com/en-us/azure/backup/quick-backup-vm-portal)
     1. fileshare
     1. sql
  * recovery services vault  
    a logical container that stores the backup data for each protected resource
* [create Recovery Services Vault](https://docs.microsoft.com/en-us/azure/backup/backup-create-rs-vault)
* create and configure [backup policy](https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)
  * retention - day/week/month/year
  * schedule - day/week
* perform a restore operation
  * [Steps of Recover from VM doc](https://docs.microsoft.com/en-us/azure/backup/backup-azure-restore-files-from-vm)
    1. click Azure portal - File Recovery from Vault
    1. Select a restore point
    1. Download & Run Script
       * ensure the script is able to access
         1. download.microsoft.com
         1. Recovery Service URLs
         1. outbound port 3260 

> [difference between Azure Backup and Azure Site Recovery](https://docs.microsoft.com/en-us/azure/backup/backup-overview#whats-the-difference-between-azure-backup-and-azure-site-recovery)  

> |backup|site recovery|
> |:-|:-|
> |backup data granular(粒状) </br> slow|access secondary location when primary location under disaster strikes </br> fast|

***

## Deploy and manage virtual machines (VMs) (15-20%)

Create and configure a VM for Windows and Linux

* configure high availability
  * recommend use Availability Set for high availability 99.95%
* configure monitoring, networking, storage, and virtual machine size
  * monitoring, need to enable **agent**, need storage account
  * [disk comparison](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#disk-comparison)
  * [Diagnostic](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostics-extension-overview#data-you-can-collect)  

* deploy and configure scale sets
  * VMSS auto-scale
    1. Network In/Out  
    1. Percentage CPU
    1. CPU Credits Remaining  
    1. CPU Credits Consumed  
    1. Disk Read/Write Bytes  
    1. Disk Read/Write Operations per second.
  * increase / decrease VMSS capacity manually  
    ```cmd
    az vmss scale \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --new-capacity 5
    ```

Automate deployment of VMs

* modify Azure Resource Manager (ARM) template  
  * publisher: the company that created and owns the product  
  * offer: product  
  * sku: version  
  ``` json
    "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "[parameters('windowsOSVersion')]",
              "version": "latest"
    }
  ```
    ``` json
    "imageReference": {
              "publisher": "RedHat",
              "offer": "RHEL",
              "sku": "7.3",
              "version": "latest"
    }
    ```
* configure location of new VMs
* [configure VHD template](https://docs.microsoft.com/en-us/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template)
  ```json
  "storageProfile": {
    "osDisk": {
        "name": "[concat(parameters('vmName'),'-osDisk')]",
        "osType": "[parameters('osType')]",
        "caching": "ReadWrite",
        "image": {
            "uri": "[parameters('vhdUrl')]"
        },
        "vhd": {
            "uri": "[variables('osDiskVhdName')]"
        },
        "createOption": "FromImage"
    }
  },
  ```
  * [upload VHD disk to Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/prepare-for-upload-vhd-image)
* deploy from template
* save a deployment as an ARM template
* deploy Windows and Linux VMs
  * switch win to linux  
  just change template publisher, image, etc..
  * [cloud-init.txt](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-automate-vm-deployment) is a widely used approach to customize a Linux VM as it boots for the first time.

Manage Azure VM

* add data disks
* add network interfaces
  * [add nic doc](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-network-interface-vm)
  * In order to add a network interface to a virtual machine, the machine needs to be stopped first.
  * change NIC setting will reboot machine
  * cannot put new NIC in different VNet when there is a NIC existed
  * VM need at least 1 NIC
* automate configuration management by using PowerShell Desired State Configuration (DSC) and VM Agent by using custom script extensions
  * DSC
     1. Configurations are declarative PowerShell scripts which define and configure instances of resources.
     1. Resources are the “make it so” part of DSC.
     1.  Local Configuration Manager (LCM) is the engine by which the DSC facilitates the interaction between resources and configuration.
  * [win agent](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/agent-windows)  
* manage VM sizes  
  * The numner of resizable VMs is different, when VM status is running or down
* move VMs from one resource group to another  
  * [move win vm](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/move-vm)
    > New resource IDs are created as part of the move. After the VM has been moved, you will need to update your tools and scripts to use the new resource IDs.
* redeploy VMs
  * If you have been facing difficulties troubleshooting  
    * RDP/SSH connection  
    * application access to Windows-based Azure virtual machine (VM)  
  * redeploy steps:  
     1. Azure will shut down the VM  
     1. move the VM to a new node within the Azure infrastructure  
     1. and then power it back on  
     1. **retaining all your configuration options and associated resource**

Manage [VM backups](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-introduction)

* configure VM backup
  * create new one
  * [restore disk](https://docs.microsoft.com/en-us/azure/backup/tutorial-restore-disk)
    * need a tmp storage account
  * replace existed VM
  * restore file
  ```bash
  az backup restore files mount-rp
  # cp something
  az backup restore files unmount-rp
  ```
* define backup policies
* implement backup policies
* perform [VM restore](https://docs.microsoft.com/en-us/azure/backup/backup-azure-arm-restore-vms)
  * create a new VM
  * Restore disk
  * Replace existing
* [Azure Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery/)

***

## Configure and manage virtual networks (30-35%)

Create connectivity between virtual networks  

* create and configure VNET [peering](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview)
  * can Global (different region)
  * Once peered, the virtual networks appear as one
  * cannot connect two VNet, where shared same ip address range  
    but can add different address
  * forward traffic  
    > VNET1 <-> VNET2, VNET2 <-> VNET3  
    > ```forward traffic on``` VNET1 <-> VNET3  
    > ```forward traffic off``` VNET1 cannot access VNET3
  * You can also configure virtual network-to-virtual network connections by using gateways, even though the virtual networks are peered.
* create and configure VNET to VNET

  ||Vnet VPN|Peering|
  |:-|:-|:-|
  |region|ok|ok|
  |subscriptions|ok|IAM|
  |classic deploy models|ok|Virtual network deployment model: Select Classic. </br> [doc](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview#next-steps)|
  |payment|bandwidth|per GB </br> global > same region|
* verify virtual network connectivity
  * NSG flow logs
    * check the flow logs for the allow and deny traffic
  * Network Watcher 
    1. connection monitor
       * provides the minimum, average, and maximum latency observed over time.
         * for example: RTT (round-trip time)
    1. IP flow verify
       * verify the flow of traffic
       * checks if a packet is allowed or denied to or from a VM
       * direction, protocol, local IP&port, remote IP&port
    1. next hub
    1. packet capture
       * diagnose network anomalies both reactively and proactively
    1. connection troubleshoot
       * test a connection at a point in time
       * also can check RTT, but just one time.
* create virtual network gateway

Implement and manage virtual networking

* configure private and public IP addresses, network routes, network interface, subnets, and virtual network

Configure [name resolution](https://docs.microsoft.com/en-us/azure/dns/)  

* configure Azure DNS
  * RecordType A, CNAME ... [wiki](https://ja.wikipedia.org/w/index.php?title=DNS%E3%83%AC%E3%82%B3%E3%83%BC%E3%83%89%E3%82%BF%E3%82%A4%E3%83%97%E3%81%AE%E4%B8%80%E8%A6%A7&oldid=72159886), [doc](https://docs.microsoft.com/en-us/azure/dns/dns-alias)
* configure custom DNS settings
* configure private and public DNS zones

Create and configure a Network Security Group (NSG)

* create security rules
  * Source
    1. any
    1. ip address
    1. **service tag**
    1. application security group
  * Destination
    1. any
    1. ip address
    1. **VirtualNetwork**
    1. application security group
* associate NSG to a subnet or network interface
  * rg -> network interface - nsg
  * rg -> VNET - subnet -> nsg
* identify required ports
* evaluate effective security rules
  * network interface - effective security rules

Implement Azure load balancer
> backend address pools -> health probes -> load balancing role -> Inbound NAT  
> each Traffic Manager endpoint must have a DNS name assigned.  

> setup load balancer
>  1. backend pools
>     * Single VM
>     * Availability set
>     * VMSS
>  1. health probes  
>  kick out vm that not work
>  1. load balance rules  
>  session: None, client IP, client IP + protocol
>  remeber access, none means each access throw to random VM.

* configure internal load balancer  
  The load balancer is located in the front-end subnet of the application.
* configure load balancing rules
  ```powershell
  $lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
  ```
* configure public load balancer
* troubleshoot load balancing

Monitor and troubleshoot virtual networking

* monitor on-premises connectivity
* use Network resource monitoring
* use Network Watcher
  * Network Watcher need be enabled in All Services -> Network Watcher
  * verfiy service endpoint. **Network Watcher** -> **Next hop**
* troubleshoot external networking
* troubleshoot virtual network connectivity

Integrate on premises network with Azure virtual network

> * Route Based VPN  
> need a route table
> * Policy Based VPN  
> for packet filtering  
> only supports one Site-to-Site VPN tunnel maximum  

* create and configure Azure VPN Gateway
  * Create Local Network Gateway
   * [Connect an on-premises network to Azure using a VPN gateway](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/vpn)
* create and configure site to site VPN
  * [Create a Site-to-Site connection](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)
    * The virtual network gateway uses specific subnet called the **gateway subnet**
      > When working with gateway subnets, avoid associating a network security group (NSG) to the gateway subnet  
      > [NSG service tag](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags)
    * configure of local network gateway
       1. **IP address**  
       This is the public IP address of the VPN device that you want Azure to connect to.  
       1. **Address Space**  
       refers to the address ranges for the network that this local network represents.
* configure Express Route
* verify on premises connectivity
* troubleshoot on premises connectivity with Azure

***

## Manage identities (15-20%)

> Difference
> * Conditional access:  
> this allows rules to be created that specifies specific criteria when signing in which can then grant access, request additional authentication or even decline the request when logging in from a platform that is denied
> * Privilege Identity:  
> this enables users to activate additional roles with their identity like Global Admin
> * MFA:  
> enabled, enforced or disabled and no automatic intelligence associated with it
> * Identity Protection:  
> associated with risky sign ins and not blocking users from logging in via specific rule sets created.

[Difference of account licenses](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis#what-are-the-azure-ad-licenses)

Manage Azure Active Directory (AD)

* add custom domains
  * rg -> AD - custom domain names
  * add txt record to name server provider
* Azure AD Join
* configure self-service password reset
  * rg -> AD - Password reset
* manage multiple directories

Manage Azure AD objects (users, groups, and devices)

* create users and groups
  * guest user can use not assigned domain
  * user must use AD domain or custom domain name
  * cannot manage the following classic subscription administrator roles in Privileged Identity Management (PIM):
     1. Account Administrator
     1. Service Administrator
     1. Co-Administrator  
     1. Roles within Exchange Online or SharePoint Online, except for Exchange Administrator and SharePoint Administrator, are not represented in Azure AD and so cannot be managed in PIM.
* manage user and group properties
  * dynamic user - add user by some rules
* manage device settings
  * [device settings doc](https://docs.microsoft.com/en-us/azure/active-directory/devices/device-management-azure-portal)
* perform bulk user updates
* manage guest accounts

Implement and manage hybrid identities

* install [Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-azure-ad-connect)
  * [Prerequisites for Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
  * For on-premise AD
    * the user need to have **Enterprise Admin** privileges
  * For the Azure AD connect side  
    * the user needs to have **Global Admin** privileges. 
    * This account must be a school or organization account and **cannot be a Microsoft account**.
* including password hash and pass-through synchronization
  * [Seamless Single Sign-On](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sso-quick-start)

  |password hash sync|pass-throngh|federation|
  |:-:|:-:|:-:|
  |Windows AD </br> (hash)-> Azure AD|through username/password to on-premises windows server AD|collection of trusted domain|
* use Azure AD Connect to configure federation with on-premises Active Directory Domain Services (AD DS)
  * [Configuring federation with AD FS](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-install-custom#configuring-federation-with-ad-fs)
* manage Azure AD Connect
  * [Azure AD Connect Health Agent](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-health-agent-install)
    * need Azure AD Premium
* manage password sync and password writeback
  * [password sync](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)
  * [password writeback](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-sspr-writeback)  
    password changed in the cloud to be written back to on-premises

Implement multi-factor authentication (MFA)

* configure user accounts for MFA  
  * [Conditional access](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/overview)
  * Get MFA
    * Azure AD Premium / 365 Business-> Conditional Access policies
    * Azure AD Free / Basic -> Conditional Access baseline protection **policies**  
    (Azure AD -> Security-Conditional Access -> baseline policy:...)
    * Azure AD Global Admin -> A subset of MFA available
  * full MFA only
    * **fraud alert**
    * **One-Time Bypass**
    * **Trusted IPs**
    * pin mode
    * MFA Reports
    * Custom greetings for phone calls
    * Custom caller ID for phone calls	
* enable MFA by using bulk update
  * **must be an Office 365 global admin**
  * MFA page -> bulk update
  * edit CSV file  
  ![MFA bulk update](https://docs.microsoft.com/en-us/office365/admin/media/2adcd052-b044-4d0c-a5e4-b859645f5ea4.png?view=o365-worldwide)
* configure fraud alerts
  * 3 versions of MFA
    * MFA for office 365
    * MFA for Azure AD Admin
      * global Admin Account only
      * enable at **Microsoft Account Setting**
    * Azure MFA(full MFA), **only version can enable Fraud alert**.
  * AD - MFA -> fraud alerts
  * allow your user to report graud, when they receive a MFA.
  * block users goto AD - MFA -> block/unblodk users.
* configure bypass options
  * [one-time bypass doc](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings#one-time-bypass)
  * authenticate a single time without performing two-step verification
  * Azure Active Directory > MFA > One-time bypass
* configure Trusted IPs, configure verification methods
  * Azure AD -> MFA -> Overview - Additional cloud-based MFA Settings
  * [trusted ip doc](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings#trusted-ips)
  * [verification methods doc](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods)
    * All AD versions can be used
