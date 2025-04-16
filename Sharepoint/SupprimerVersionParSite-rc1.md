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

# Connexion interactive au site SharePoint Online
# Permet à l'utilisateur de s'authentifier
Connect-PnPOnline -Url $SiteURL -Interactive -ClientId "XXXX6XX-XXX6X-XXXXX"

# Récupérer tous les fichiers de la bibliothèque spécifiée
# PageSize permet de limiter la récupération en lots pour éviter des délais trop longs
$Files = Get-PnPListItem -List $LibraryName -PageSize 500

# Parcourir chaque fichier récupéré dans la bibliothèque
foreach ($File in $Files) {
    try {
        # Afficher le fichier en cours de traitement
        Write-Host "Traitement du fichier : $($File.FieldValues.FileLeafRef)..."

        # Charger les propriétés du fichier, dont son URL relative
        $FileObj = Get-PnPProperty -ClientObject $File -Property File

        # Vérifier que l'objet fichier existe et qu'il possède une URL relative
        if ($FileObj -and $FileObj.ServerRelativeUrl) {
            # Charger explicitement toutes les versions du fichier
            $FileVersions = Get-PnPProperty -ClientObject $FileObj -Property Versions

            # Afficher le nombre total de versions du fichier
            Write-Host "Nombre de versions : $($FileVersions.Count)"

            # Si le fichier a plus de $MaxVersions, supprimer les versions excédentaires
            if ($FileVersions.Count -gt $MaxVersions) {
                Write-Host "Suppression des anciennes versions..."

                # Sélectionner les versions les plus anciennes à supprimer
                $VersionsToDelete = $FileVersions | Select-Object -First ($FileVersions.Count - $MaxVersions)

                # Parcourir chaque version à supprimer
                foreach ($Version in $VersionsToDelete) {
                    # Afficher la version en cours de suppression
                    Write-Host "Suppression de la version : $($Version.VersionLabel)"
                    # Supprimer la version
                    $Version.DeleteObject()
                }

                # Exécuter toutes les suppressions en attente dans SharePoint
                Invoke-PnPQuery
            }
        }
    } catch {
        # En cas d'erreur, afficher un message avec le fichier concerné
        Write-Host "Erreur pour le fichier : $($File.FieldValues.FileLeafRef) - $_" -ForegroundColor Red
    }
}


# Déconnexion de SharePoint
Disconnect-PnPOnline
Write-Host "Nettoyage terminé !"
```

