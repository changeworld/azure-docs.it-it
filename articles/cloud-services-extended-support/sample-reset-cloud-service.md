---
title: Esempi di Azure PowerShell-reimpostare i servizi cloud di Azure (supporto esteso)
description: Script di esempio per la reimpostazione di una distribuzione del servizio cloud di Azure (supporto esteso)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475321"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Reimpostare un servizio cloud di Azure (supporto esteso) 
Questi esempi illustrano vari modi per reimpostare una distribuzione di servizio cloud di Azure (supporto esteso) esistente.

## <a name="reimage-role-instances-of-cloud-service"></a>Ricreare l'immagine delle istanze del ruolo del servizio cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Questo comando consente di ricreare l'immagine di due istanze del ruolo ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1 del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Ricreare l'immagine di tutti i ruoli del servizio cloud
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Questo comando consente di ricreare l'immagine di tutte le istanze del ruolo del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Ricreare l'immagine di una singola istanza del ruolo di un servizio cloud
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Questo comando ricrea l'immagine dell'istanza del ruolo denominata ContosoFrontEnd_IN_0 del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Ricompilare le istanze del ruolo del servizio cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Questo comando Ricompila due istanze del ruolo ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1 del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Ricompila tutti i ruoli del servizio cloud
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Questo comando consente di ricompilare tutte le istanze del ruolo del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="restart-role-instances-of-cloud-service"></a>Riavviare le istanze del ruolo del servizio cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Questo comando Riavvia 2 istanze del ruolo ContosoFrontEnd_IN_0 e ContosoBackEnd_IN_1 del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="restart-all-roles-of-cloud-service"></a>Riavvia tutti i ruoli del servizio cloud
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Questo comando Riavvia tutte le istanze del ruolo del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui servizi cloud di Azure (supporto esteso), vedere [Panoramica di servizi cloud di Azure (supporto esteso)](overview.md).
- Visitare il [repository di esempi di servizi cloud (supporto esteso)](https://github.com/Azure-Samples/cloud-services-extended-support)
