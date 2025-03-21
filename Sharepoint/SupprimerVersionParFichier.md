## Supprimer toutes les versions d'un fichier 

Ici on supprime toutes les versions en ne conservant que 50

```powershell
Install-Module PnP.PowerShell -Scope CurrentUser
# Importer le module PnP PowerShell
Import-Module PnP.PowerShell

# Définir les variables
$SiteURL = "https://tenant.sharepoint.com/sites/ton-site"
$FileRelativeURL = "/sites/ton-site/Documents partages/chemin/vers/ton/fichier.extension-du-fichier"  # URL relative du fichier 
$MaxVersions = 50  # Nombre de versions à conserver

# Connexion à SharePoint Online
#Connect-PnPOnline -Url $SiteURL -Interactive
Connect-PnPOnline -Url $SiteURL -UseWebLogin
# Récupérer le fichier spécifique
$File = Get-PnPFile -Url $FileRelativeURL -AsListItem

if ($File) {
    # Charger la propriété File pour récupérer les versions
    $FileObj = Get-PnPProperty -ClientObject $File -Property File
    $FileVersions = Get-PnPProperty -ClientObject $FileObj -Property Versions

    if ($FileVersions.Count -gt $MaxVersions) {
        Write-Host "Suppression des anciennes versions pour : $FileRelativeURL"

        # Récupérer uniquement les anciennes versions à supprimer
        $VersionsToDelete = $FileVersions | Select-Object -First ($FileVersions.Count - $MaxVersions)

        foreach ($Version in $VersionsToDelete) {
            $Version.DeleteObject()
        }

        # Exécuter la suppression en batch
        Invoke-PnPQuery
        Write-Host "Nettoyage terminé pour $FileRelativeURL"
    }
    else {
        Write-Host "Le fichier a $($FileVersions.Count) versions, aucune suppression nécessaire."
    }
}
else {
    Write-Host "Fichier introuvable : $FileRelativeURL"
}

# Déconnexion de SharePoint
#Disconnect-PnPOnline
Write-Host "Script terminé !"
```
