---
title: "[Disaster Recovery] How to migrate Azure AD Connect"
datePublished: Wed Aug 02 2023 06:09:20 GMT+0000 (Coordinated Universal Time)
cuid: clktbvkfs000109l4935o3lem
slug: disaster-recovery-how-to-migrate-azure-ad-connect
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/M5tzZtFCOfs/upload/1d13e6fca1a58c97bed7bb552d5c0509.jpeg
tags: cloud, networking, disaster-recovery

---

# *What is Azure AD Connect?*

Simply put, Azure AD Connect is a solution to automatically synchronize identity data between their on-premises Active Directory environment and Azure AD. That way, users can use a single identity to access on-premises applications and cloud services such as Microsoft 365.

* It includes a number of technologies:
    
* Azure AD Connect Sync
    
* Azure AD Connect Health
    
* ADFS (Active Directory Federation Services)
    
* The PHS/PTA/SSSO Provisioning Connector
    

Azure AD Connect supports integration with other Microsoft products such as Office365, Sharepoint, Dynamics CRM, and Outlook.

Alternatively, you can also consider the cloud-managed solution: Azure AD Connect cloud sync.

# *What happens if you lose your Azure AD sync?*

If you lose your Azure AD sync, it depends on the type of sync you are using.

If you sync password hashes, you can still connect to O365 without issue.

If you use Pass-through Authentication, you’re dead, no access to O365.

When the sync is interrupted, you will not be able to make changes to the on-premises Active Directory and those changes will not be synchronized to Azure AD until you restore connectivity.

# *How to migrate existing Azure AD connect after a network disaster?*

## *Create the same AD server*

Firstly, make sure we have the same AD server with all the identities.

Create an AD with the same domain name. I have my local domain as "clayton.local"

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/5ab60de705f2ae9d9a699a4b5da3863f8d33dfb1b8b31282922aedea80be9611.webp align="left")

Go to Tool &gt; Active Directory Domain and Trust.

Right-click the domain, choose Properties, and Add Alternative Suffixes.

I use my own: "[susuomlu.com](http://susuomlu.com)". This will be our UPN.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/5144b9592a454b9a4490ba31cbbe0ff7a629244b29bd37ff95b991f8352f63ee.webp align="left")

## *Manually create users with the same SIDs*

Go to [portal.azure.com](http://portal.azure.com) &gt; Azure Active Directory &gt; Users and select users that need to be synchronized.

Click on 'Properties' and you can see their "On-premises immutable ID" attribute's value.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/7db10c1717b193fafb36c71db80984d02ddce11f67408110e204129e5d34cc9f.webp align="left")

Or, we can get all IID using the script below:

```haskell
Install-Module MSOnline
Import-Module MSOnline
Connect-MsolService
$onlineusers = Get-MsolUser -All | Select-Object UserprincipalName,ImmutableID,LastDirSyncTime| Export-Csv c:\IID.csv -NoTypeInformation
```

Run the script to get all IID of users, this .csv file is stored at C:\\IID.csv

## *Create the unique SID from Immutable ID*

Run this PowerShell Script, and replace the list with the immutable ID from the .csv file.

```haskell
$IID_List = @('dMIj64cN/0CM9fmLIexC4g==','I4VwJomrjUWzPHk6sMlh3g==','rT8wJf+DrkCcKVxXX7ADzA==','vqhLzR9Mq0aEvHed8eA00Q==') ### You can put more into this list
Function Convert_IID_to_SID ($IID){
    $b64 = $IID
    $bytes = [System.Convert]::FromBase64String($b64)
    $hex = New-Object -TypeName System.Text.StringBuilder -ArgumentList ($bytes.Length * 2)
    foreach ($byte in $bytes) {
    $hex.AppendFormat("{0:x2}", $byte) > $null
    }
    $hex.ToString().ToUpper()
}
Foreach ($item in $IID_List) {
    Convert_IID_to_SID($item)
}
```

After the run, 32-Digit values should appear. Copy them for later use.

Please carefully check if the SID is correct for each user.

## *Create new users with the same unique Immutable ID*

Create users, using the UPN.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/97941b74ff5d6d5296604c61af7c1d2c9b06a2aa057858c21244f9707d84326f.webp align="left")

Go to View and Enable Advanced Features

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/06111f1d3d3e8ca573fd872856924642f7568128d63427edfad2d9e9d18acc79.webp align="left")

Right Click on the User, go to Properties &gt; Attribute Editor, and find the "ms-DS-ConsistencyGuid" attribute.

Select Edit/Modify for the attribute.

Paste the 32-Digit string from before.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/cc737de5a00d7a3c22f6266183acf43526bbdabdc99fcdf4c49696fdd8ff725f.webp align="left")

Apply and OK.

You will have to do this for all the users, otherwise, they will be duplicated.

Now, you can install Azure AD Connect and start the sync process again.

# *The result*

This action will create a Service account.

If the SID was modified correctly, other user accounts will not be duplicated.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/5f21322bc64f6d4a38caabb30dd482799ee41a7ab87596e6adb14d840156a78c.webp align="left")

On Azure Portal, check if  Sync Status is Enabled and Last Synced recently.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/b7c33b44257994c9f8d399d38b0d4bd58ff764d4e7922e91da3a00382c94af65.webp align="left")

After this, you have successfully migrated your existing Azure AD connect!