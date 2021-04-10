---
title: Aggiornare servizi multimediali V3 dopo le chiavi di accesso alle archiviazione in sequenza | Microsoft Docs
description: Questo articolo fornisce indicazioni su come aggiornare servizi multimediali V3 dopo aver eseguito il rollup delle chiavi di accesso alle archiviazione.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 76ba1b848b033c5a26e81be18bef23a2b4715a7e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572399"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Aggiornare servizi multimediali V3 dopo aver eseguito il rollup delle chiavi di accesso alle archiviazione

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Viene richiesto di selezionare un account di archiviazione di Azure quando si crea un nuovo account di servizi multimediali di Azure (AMS).  È possibile aggiungere più di un account di archiviazione all'account di servizi multimediali. Questo articolo illustra come far ruotare le chiavi di archiviazione. Viene inoltre illustrato come aggiungere gli account di archiviazione a un account multimediale.

Per completare le azioni descritte in questo articolo, è necessario usare [Azure Resource Manager API](/rest/api/media/operations/azure-media-services-rest-api-reference) e [PowerShell](/powershell/module/az.media).  Per altre informazioni, vedere [come gestire le risorse di Azure con PowerShell e gestione risorse](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Generazione della chiave di accesso alle archiviazione

Quando viene creato un nuovo account di archiviazione, Azure genera chiavi di accesso alle archiviazioni a 2 512 bit che vengono usate per autenticare l'accesso all'account di archiviazione. Per rendere più sicure le connessioni di archiviazione, rigenerare e ruotare periodicamente la chiave di accesso alle archiviazione. Vengono fornite due chiavi di accesso (primaria e secondaria) per consentire di mantenere le connessioni all'account di archiviazione usando una chiave di accesso mentre si rigenera l'altra chiave di accesso. Questa procedura viene anche denominata "rollover delle chiavi di accesso".

Servizi multimediali dipende da una chiave di archiviazione fornita. In particolare, i localizzatori che sono usati per trasmettere in streaming o scaricare gli asset dipendono dalla chiave di accesso alle risorse di archiviazione specificata. Quando viene creato un account AMS, per impostazione predefinita prende una dipendenza dalla chiave di accesso alle archiviazione primaria. Tuttavia, gli utenti possono aggiornare la chiave di archiviazione di AMS. È necessario lasciare che servizi multimediali conosca la chiave da usare attenendosi alla procedura seguente:

>[!NOTE]
> Se si dispone di più account di archiviazione, è necessario eseguire questa procedura per ogni account di archiviazione. L'ordine in cui ruotare le chiavi di archiviazione non è prefissato. È possibile ruotare prima la chiave secondaria e quindi quella principale o viceversa.
>
> Prima di eseguire i passaggi in un account di produzione, assicurarsi di testarli in un account di pre-produzione.
>

## <a name="steps-to-rotate-storage-keys"></a>Passaggi per ruotare le chiavi di archiviazione
 
 1. Modificare la chiave primaria dell'account di archiviazione tramite il cmdlet di PowerShell o il portale di [Azure](https://portal.azure.com/) .
 2. Chiamare il `Sync-AzMediaServiceStorageKeys` cmdlet con i parametri appropriati per forzare l'account multimediale a prelevare le chiavi dell'account di archiviazione
 
    Nell'esempio seguente viene illustrato come sincronizzare le chiavi con gli account di archiviazione.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Attendere circa un'ora. Verificare che gli scenari di streaming funzionino.
 4. Modificare la chiave secondaria dell'account di archiviazione tramite il cmdlet di PowerShell o portale di Azure.
 5. Chiamare `Sync-AzMediaServiceStorageKeys` PowerShell con i parametri appropriati per forzare l'account multimediale a prelevare nuove chiavi dell'account di archiviazione.
 6. Attendere circa un'ora. Verificare che gli scenari di streaming funzionino.
 
### <a name="a-powershell-cmdlet-example"></a>Esempio di cmdlet di PowerShell

Nell'esempio seguente viene illustrato come ottenere l'account di archiviazione e sincronizzarlo con l'account AMS.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Passaggi per aggiungere gli account di archiviazione all'account AMS

L'articolo seguente illustra come aggiungere gli account di archiviazione all'account AMS: [Collegare più account di archiviazione a un account di Servizi multimediali](media-services-managing-multiple-storage-accounts.md).
