---
title: Monitoraggio diagnostica di Azure-attestazione di Azure
description: Monitoraggio diagnostica di Azure per l'attestazione di Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726479"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Impostazione della diagnostica con l'endpoint Trusted Platform Module (TPM) dell'attestazione di Azure

I [log della piattaforma](../azure-monitor/essentials/platform-logs-overview.md) in Azure, inclusi i log attività e i log delle risorse di Azure, forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono. Le [metriche della piattaforma](../azure-monitor/essentials/data-platform-metrics.md) vengono raccolte per impostazione predefinita e vengono in genere archiviate nel database di metriche di monitoraggio di Azure. Questo articolo fornisce informazioni dettagliate sulla creazione e la configurazione delle impostazioni di diagnostica per inviare le metriche della piattaforma e i log della piattaforma a destinazioni diverse. 

Il servizio endpoint TPM è abilitato con l'impostazione di diagnostica e può essere usato per monitorare l'attività. Per configurare il [monitoraggio di Azure](../azure-monitor/overview.md) per l'endpoint del servizio TPM usando PowerShell, seguire questa procedura. 

Configurare il servizio di attestazione di Azure. 

[Configurare Attestazione di Azure con Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
I log attività sono disponibili nella sezione contenitori dell'account di archiviazione. Informazioni dettagliate sono disponibili in [raccogliere i log delle risorse da una risorsa di Azure e analizzarli con monitoraggio di Azure-monitoraggio di Azure](../azure-monitor/essentials/tutorial-resource-logs.md)
