## Définir une politique de versions sur un site

Ici les fichier sont supprimés au delà de 15 Versions. Sur le centre d'admin sharepoint on ne peut pas descendre en dessous de 100. Donc le script est pratique

D'autres tutos que j'ai trouvé ; 

https://www.sharepointdiary.com/2019/07/sharepoint-online-set-versioning-limit-using-powershell.html

https://lazyadmin.nl/office-365/version-history-limits

```powershell
Install-Module PnP.PowerShell -Scope CurrentUser
Import-Module PnP.PowerShell

$SiteURL = "https://tenant.sharepoint.com/sites/ton-site 
$LibraryName = "Documents"
 
#Connect to SharePoint Online site
Connect-PnPOnline $SiteURL -UseWebLogin
 
#Get the Library
$Library = Get-PnPList -Identity $LibraryName
 
#Set Version History Limit
Set-PnPList -Identity $Library -MajorVersions 15
```
