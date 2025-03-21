# Utilisation de SharePoint Online Management Shell avec PowerShell 7

## Introduction

Ce document fournit des instructions sur l'installation et l'importation du module SharePoint Online Management Shell à l'aide de PowerShell 7.

## Prérequis

- Avoir PowerShell 7 installé sur votre machine.
- Avoir les autorisations nécessaires pour installer des modules PowerShell.

## Installation du module

Pour installer le module SharePoint Online Management Shell, exécutez la commande suivante dans PowerShell 7 :

``powershell
Install-Module -Name MICROSOFT.Online.SharePoint.PowerShell
Importation du module
Après avoir installé le module, vous devez l'importer. Utilisez la commande suivante :

Import-Module MICROSOFT.Online.SharePoint.PowerShell -UseWindowsPowerShell
Notes
L'utilisation de -UseWindowsPowerShell permet d'importer le module dans un contexte qui prend en charge les fonctionnalités de SharePoint Online Management Shell.
Assurez-vous d'exécuter PowerShell en tant qu'administrateur pour éviter les problèmes de permission lors de l'installation du module.
Conclusion
Vous êtes maintenant prêt à utiliser le SharePoint Online Management Shell avec PowerShell 7. Vous pouvez commencer à exécuter des commandes pour gérer vos sites SharePoint Online.
``

Vous pouvez copier ce texte dans un fichier avec l'extension .md, et il sera formaté correctement pour une visualisation en Markdown.




