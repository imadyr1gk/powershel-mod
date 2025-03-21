## Les fichiers les plus volumineux d'un site Sharepoint en spécifiant une taille minimale de recherche

Il faut se connecter au site avant de pouvoir exécuter le script avec cette commande  : 
```powershell
Connect-PnPOnline -Url "https://tenant.sharepoint.com/sites/LE-SITE-a-analyser" -UseWebLogin
```
   Pensez à modifier le nom du fichier CSV sortie
   Si besoin modifier la taille minimum servant de repère : le MinFileSize

Les informations extraites pour chaque fichier sont stockées dans un tableau ($FileData) sous forme d'objets personnalisés. Les informations collectées pour chaque fichier incluent :

Nom du fichier (FileName) : le nom du fichier.

URL (URL) : le chemin complet du fichier dans la bibliothèque.

Date de création (Created) : la date à laquelle le fichier a été créé.

Date de modification (Modified) : la date de la dernière modification du fichier.

Taille du fichier (FileSize) : la taille du fichier en Mo. 

Le _MinFileSize_ , ce ont les fichiers au dessus de cette taille qui seront afficher dans le csv. A modifier au bon vouloir

Taille totale (TotalSize) : la taille totale ( inclus les versions et les métadonnées).

Dernière version (LastVersion) : la version du fichier.

```powershell
Install-Module PnP.PowerShell -Scope CurrentUser

Import-Module PnP.PowerShell

$SiteURL = "https://tenant.sharepoint.com/sites/tenant-site"
$ListName = "Documents"
$CSVFilePath = "$env:userprofile\FundLarge-"
$MinFileSize = 100#100 MB
   
#Connect to PnP Online
Connect-PnPOnline -Url $SiteURL -UseWebLogin
  
#Get the Document Library
$List = Get-PnPList -Identity $ListName
   
$global:counter=0
$ListItemCount = $List.ItemCount
#Get All Items from the List
$ListItems = Get-PnPListItem -List $ListName -Fields FileRef, SMTotalFileStreamSize, SMTotalSize,_UIVersionString -PageSize 2000 -ScriptBlock { Param($items) $global:counter += $items.Count; Write-Progress `
                -PercentComplete ($global:Counter / ($ListItemCount) * 100) -Activity "Getting Files of '$($List.Title)'" `
                    -Status "Processing Files $global:Counter of $($ListItemCount)";} | Where {($_.FileSystemObjectType -eq "File")}
Write-Progress -Activity "Completed Retrieving Files!" -Completed
 
$FileData = @()
$ItemCounter = 1
$TotalFiles = $ListItems.count
 #Collect data from each files
ForEach ($Item in $ListItems)
{
     #Display a Progress bar
    Write-Progress -Activity "Processing Files ($ItemCounter of $TotalFiles)" -Status "Processing File: $($Item.FieldValues.FileRef)'" -PercentComplete (($ItemCounter / $TotalFiles) * 100)
   
    If (($Item.FieldValues.SMTotalSize.LookupId/1MB) -gt $MinFileSize)
    {
        write-host $Item.FieldValues.FileRef
        $FileData += [PSCustomObject][ordered]@{
            FileName         = $Item.FieldValues.FileLeafRef
            URL              = $Item.FieldValues.FileRef
            Created          = $Item.FieldValues.Created
            Modified         = $Item.FieldValues.Modified
            FileSize         = [math]::Round(($Item.FieldValues.SMTotalFileStreamSize/1MB),2)
            TotalSize        = [math]::Round(($Item.FieldValues.SMTotalSize.LookupId/1MB),2)
            LastVersion      = $Item.FieldValues._UIVersionString
            }
     }
     $ItemCounter++
 }
#Export Files data to CSV File
$FileData | Export-Csv -Path $CSVFilePath -NoTypeInformation
```
