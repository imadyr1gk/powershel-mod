## Supprimer toutes les versions des fichiers de la bibliothèque documents d'un sites Sharepoint et n'en conserver qu'un nombre donnée

Dans notre exemple on n'en conserve que 32

```powershell
Install-Module PnP.PowerShell -Scope CurrentUser
# Importer le module PnP PowerShell
Import-Module PnP.PowerShell

# Définir les variables
$SiteURL = "https://tenant.sharepoint.com/sites/ton-site"
$LibraryName = "Documents"  # Nom de la bibliothèque
$MaxVersions = 32  # Nombre de versions à conserver

# Connexion à SharePoint Online
#Connect-PnPOnline -Url $SiteURL -UseWebLogin

# Récupérer tous les fichiers de la bibliothèque
$Files = Get-PnPListItem -List $LibraryName -PageSize 500

foreach ($File in $Files) {
    # Charger la propriété File
    $FileObj = Get-PnPProperty -ClientObject $File -Property File

    if ($FileObj -and $FileObj.ServerRelativeUrl) {
        # Charger explicitement les versions du fichier
        $FileVersions = Get-PnPProperty -ClientObject $FileObj -Property Versions

        if ($FileVersions.Count -gt $MaxVersions) {
            Write-Host "Suppression des anciennes versions pour : $($File.FieldValues.FileLeafRef)"

            # Supprimer les versions les plus anciennes en gardant les $MaxVersions dernières
            $VersionsToDelete = $FileVersions | Select-Object -First ($FileVersions.Count - $MaxVersions)

            foreach ($Version in $VersionsToDelete) {
                Write-Host "Suppression de la version : $($Version.VersionLabel)"
                $Version.DeleteObject()
            }

            # Exécuter la suppression
            Invoke-PnPQuery
        }
    }
}

# Déconnexion de SharePoint
#Disconnect-PnPOnline
Write-Host "Nettoyage terminé !"
```
