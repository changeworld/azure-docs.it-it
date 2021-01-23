---
title: Creare un contenitore di servizi cloud (classico) con PowerShell | Microsoft Docs
description: Questo articolo illustra come creare un contenitore del servizio cloud con PowerShell. Il contenitore ospita il ruolo Web e il ruolo di lavoro.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a8f06ce08c0df4cc86afe6fbbe7eb12fd866e61c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743271"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Usare un comando Azure PowerShell per creare un contenitore di servizi cloud (classico) vuoto

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

Questo articolo illustra come creare velocemente un contenitore di Servizi cloud usando i cmdlet di Azure PowerShell. Eseguire i passaggi seguenti:

1. Installare i cmdlet di Microsoft Azure PowerShell dalla pagina dei [download di Azure PowerShell](https://aka.ms/webpi-azps) .
2. Aprire il prompt dei comandi di PowerShell.
3. Usare [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-4.0.0&preserve-view=true) per accedere.

   > [!NOTE]
   > Per altre istruzioni sull'installazione del cmdlet di Azure PowerShell e sulla connessione alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).
   >
   >
4. Usare il cmdlet **New-AzureService** per creare un contenitore del servizio cloud vuoto.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Per richiamare il cmdlet, seguire questo esempio:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Per altre informazioni sulla creazione del servizio cloud di Azure, eseguire:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Passaggi successivi

* Per gestire la distribuzione del servizio cloud, vedere i comandi [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService?view=azuresmps-4.0.0&preserve-view=true), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService?view=azuresmps-4.0.0&preserve-view=true) e [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice?view=azuresmps-4.0.0&preserve-view=true). Per altre informazioni, è anche possibile vedere [Come configurare i servizi cloud](cloud-services-how-to-configure-portal.md) .
* Per pubblicare il progetto del servizio cloud in Azure, vedere l'esempio di codice **PublishCloudService.ps1** riportato nel [repository archiviato per i servizi cloud](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).