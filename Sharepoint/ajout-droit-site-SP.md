## Ajouter des droits à tous les site sharepoints pour effectuer des tâches par exemple

```powershell
Install-Module -Name Microsoft.Online.SharePoint.PowerShell
Import-Module Microsoft.Online.SharePoint.PowerShell -UseWindowsPowerShell
$AdminSiteUrl = "https://votre-tenant-admin.sharepoint.com"
Connect-SPOService -Url $AdminSiteUrl 
$adminEmail = "xxx@domain.tld"
$sites = Get-SPOSite -Limit All

foreach ($site in $sites) {
    Set-SPOUser -Site $site.Url -LoginName $adminEmail -IsSiteCollectionAdmin $true
    Write-Host "Administrateur global ajouté au site : $($site.Url)"
}
```

Pour retirer changer $true par $false

## Ajout droit à un seul site

```powershell
$SiteURL = "https://tenant.sharepoint.com/sites/ton-site" 
Connect-SPOService -Url $SiteURL
Set-SPOUser -Site $SiteURL -LoginName xxxx@domain.tld -IsSiteCollectionAdmin $true
```

Pour retirer changer $true par $false
