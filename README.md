# Fix Azure AD sync when soft (SMTP) and hard match (objectGUID) fail

Even though this shouldn't happen often, I had to write a script for this use case.

The tenantID variable is used because this script runs using Delegated Access Permissions.

I threw this together in about an hour, could use improvements and cleaning up.

## Warning

This operation can have heavy consequences on Azure AD synchronisation. This may change many attributes in ways you don't expect and should proceed with extreme caution before running this script.

[Read this post](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-connect-dealing-with-incorrectly-created-users-post/m-p/220504)

# Use the script
```
Fix-ADSyncMissingImmutableID.ps1 [-UserPrincipalName] <string> [-TenantID] <string> [-ADObjectGUID] <string> [<CommonParameters>]
```
 
## Check if everything went correctly

```Powershell
# Import users in a var named Users containing UPN and ObjectGUID.

Get-MsolUser -TenantId $tenantID | select UserprincipalName, immutableID, @{name="ADbase64ObjectGUID" ;  expression = {[system.Convert]::ToBase64String(( [GUID]($Users | Where userprincipalname -eq $_.Userprincipalname | select -ExpandProperty ObjectGUID)).ToByteArray() ) } } | where ADbase64ObjectGUID -NotLike "AAA*"
```