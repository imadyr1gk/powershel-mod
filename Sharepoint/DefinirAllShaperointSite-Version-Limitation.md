## Définir une politique de versions sur tous les sites Sharepoint

S'assurer au préalable d'avoir les droits sur tous les sites avec le compte qui compte effectuer la tache. Si pas de droits voir : https://github.com/imadyr1gk/powershel-mod/blob/main/Sharepoint/ajout-droit-site-SP.md

Ici on limite à 10 le versionnage

Ce script utilise le module PnP et le module Microsoft Harepoint Online

Il est impératif d'être en powershell 7 minimum oiur executer le module PnP. ( https://pnp.github.io/powershell/ )

Une fois en powershell 7, il se peut que le module Microsoft Harepoint Online (SPO) ne soit pas fonctionnel. Pour cela voir : https://github.com/imadyr1gk/powershel-mod/blob/main/SPO-in-PWSH7.md
```powershell
Install-Module -Name Microsoft.Online.SharePoint.PowerShell
Import-Module Microsoft.Online.SharePoint.PowerShell

# URL d'administration SharePoint
$AdminUrl = "https://tenant-admin.sharepoint.com"  # Remplace par l'URL de ton admin center SharePoint

# Connexion à SharePoint Online

Connect-SPOService -Url $AdminUrl 

# Récupérer tous les sites SharePoint Online
$sites = Get-SPOSite -Limit All

# Boucle pour parcourir chaque site
foreach ($site in $sites) {
    Write-Host "Traitement du site : $($site.Url)" -ForegroundColor Cyan
    
    # Connexion au site SharePoint en utilisant PnP
    Connect-PnPOnline -Url $site.Url -UseWebLogin

    # Essayer de récupérer la bibliothèque "Documents"
    try {
        $Library = Get-PnPList -Identity "Documents"
        Write-Host "Bibliothèque trouvée sur le site $($site.Url)" -ForegroundColor Green
        Set-PnPList -Identity $Library -MajorVersions 10
        # Effectuer les actions souhaitées sur la bibliothèque ici
        # Par exemple, activer le versionning, etc.
        # Exemple : Set-PnPList -Identity $Library -EnableVersioning $true -MajorVersions 10
    }
    catch {
        Write-Host "La bibliothèque 'Documents' n'a pas été trouvée sur le site $($site.Url)" -ForegroundColor Red
    }
}

Write-Host "Traitement terminé pour tous les sites." -ForegroundColor Green

```
