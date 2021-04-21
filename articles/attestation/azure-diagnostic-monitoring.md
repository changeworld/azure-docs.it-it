---
title: Monitoraggio diagnostico di Azure per attestazione di Azure
description: Monitoraggio diagnostico di Azure per attestazione di Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b0cd0402348e4aa45b291f30b677fc9e4bbdb98
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833635"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Configurare la diagnostica con un endpoint Trusted Platform Module (TPM) di attestazione di Azure

Questo articolo illustra come creare e configurare le impostazioni di diagnostica per inviare le metriche della piattaforma e i log della piattaforma a destinazioni diverse. [I log della](/azure/azure-monitor/platform/platform-logs-overview) piattaforma in Azure, inclusi il log attività di Azure e i log delle risorse, forniscono informazioni di diagnostica e controllo dettagliate per le risorse di Azure e la piattaforma Azure da cui dipendono. [Le metriche della](/azure/azure-monitor/platform/data-platform-metrics) piattaforma vengono raccolte per impostazione predefinita e archiviate nel database Monitoraggio di Azure Metrics.

Prima di iniziare, assicurarsi di aver [configurato attestazione di Azure con Azure PowerShell](quickstart-powershell.md).

Il servizio Trusted Platform Module (TPM) è abilitato nelle impostazioni di diagnostica e può essere usato per monitorare l'attività. Configurare Monitoraggio [di Azure per](/azure/azure-monitor/overview) l'endpoint di servizio TPM usando il codice seguente.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

I log attività sono disponibili nella **sezione Contenitori** dell'account di archiviazione. Per altre informazioni, vedere [Raccogliere e analizzare i log delle risorse da una risorsa di Azure.](/azure/azure-monitor/learn/tutorial-resource-logs)
