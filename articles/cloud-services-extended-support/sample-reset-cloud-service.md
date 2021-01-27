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
ms.openlocfilehash: 40b44fd277eac14a5bf2c15f58fccfd9d5b156c4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881486"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Reimpostare un servizio cloud di Azure (supporto esteso) 
Questi esempi illustrano vari modi per reimpostare una distribuzione di servizio cloud di Azure (supporto esteso) esistente.

## <a name="reimage-role-instances-of-cloud-service"></a>Ricreare l'immagine delle istanze del ruolo del servizio cloud
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
Questo comando ricrea l'immagine di due istanze del ruolo **ContosoFrontEnd \_ in \_ 0** e **ContosoBackEnd \_ in \_ 1** del servizio cloud denominato ContosoCS che appartiene al gruppo di risorse denominato ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Ricreare l'immagine di tutti i ruoli del servizio cloud
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Ricreare l'immagine di una singola istanza del ruolo di un servizio cloud
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Riavviare una singola istanza del ruolo di un servizio cloud
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui servizi cloud di Azure (supporto esteso), vedere [Panoramica di servizi cloud di Azure (supporto esteso)](overview.md).
- Visitare il [repository di esempi di servizi cloud (supporto esteso)](https://github.com/Azure-Samples/cloud-services-extended-support)