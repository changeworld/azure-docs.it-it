---
title: Monitoraggio diagnostica di Azure per l'attestazione di Azure
description: Monitoraggio diagnostica di Azure per l'attestazione di Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168349"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Configurare la diagnostica con un endpoint Trusted Platform Module (TPM) di attestazione di Azure

Questo articolo consente di creare e configurare le impostazioni di diagnostica per inviare le metriche della piattaforma e i log della piattaforma a destinazioni diverse. I [log della piattaforma](/azure/azure-monitor/platform/platform-logs-overview) in Azure, inclusi i log attività e i log delle risorse di Azure, forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono. Le [metriche della piattaforma](/azure/azure-monitor/platform/data-platform-metrics) vengono raccolte per impostazione predefinita e vengono archiviate nel database di metriche di monitoraggio di Azure.

Prima di iniziare, assicurarsi [di aver configurato l'attestazione di Azure con Azure PowerShell](quickstart-powershell.md).

Il servizio endpoint Trusted Platform Module (TPM) è abilitato nelle impostazioni di diagnostica e può essere usato per monitorare l'attività. Configurare il [monitoraggio di Azure](/azure/azure-monitor/overview) per l'endpoint del servizio TPM usando il codice seguente.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

I log attività si trovano nella sezione **contenitori** dell'account di archiviazione. Per altre informazioni, vedere [raccogliere e analizzare i log delle risorse da una risorsa di Azure](/azure/azure-monitor/learn/tutorial-resource-logs).
