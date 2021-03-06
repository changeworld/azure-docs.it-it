---
title: Configurare la preferenza di routing di rete
titleSuffix: Azure Storage
description: Configurare le preferenze di routing di rete per l'account di archiviazione di Azure per specificare in che modo il traffico di rete viene instradato all'account dai client tramite Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790456"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configurare le preferenze di routing di rete per Archiviazione di Azure

Questo articolo descrive come configurare le preferenze di routing di rete e gli endpoint specifici della route per l'account di archiviazione. 

La preferenza di routing di rete specifica il modo in cui il traffico di rete viene instradato all'account dai client tramite Internet. Gli endpoint specifici della route sono nuovi endpoint creati Archiviazione di Azure per l'account di archiviazione. Questi endpoint indirizzano il traffico su un percorso desiderato senza modificare le preferenze di routing predefinite. Per altre informazioni, vedere [Preferenza di routing di rete per Archiviazione di Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configurare la preferenza di routing per l'endpoint pubblico predefinito

Per impostazione predefinita, la preferenza di routing per l'endpoint pubblico dell'account di archiviazione è impostata su Rete globale Microsoft. Come preferenza di routing predefinita per l'endpoint pubblico dell'account di archiviazione, è possibile scegliere tra la rete globale Microsoft e il routing Internet. Per altre informazioni sulla differenza tra questi due tipi di routing, vedere Preferenza di routing di [rete per](network-routing-preference.md)Archiviazione di Azure . 

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per modificare la preferenza di routing in Routing Internet:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'account di archiviazione nel portale.

3. In **Impostazioni** scegliere **Rete**.

    > [!div class="mx-imgBorder"]
    > ![Opzione di menu Rete](./media/configure-network-routing-preference/networking-option.png)

4.  Nella scheda **Firewall e reti virtuali,** in Routing **di rete**, modificare l'impostazione delle **preferenze Routing** in Routing **Internet**.

5.  Fare clic su **Salva**.

    > [!div class="mx-imgBorder"]
    > ![Opzione di routing Internet](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate per eseguire l'autenticazione.

   ```powershell
   Connect-AzAccount
   ```

2. Se la propria identità è associata a più sottoscrizioni, impostare come sottoscrizione attiva quella dell’account di archiviazione destinato a ospitare il sito Web statico.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Per modificare la preferenza di routing in Routing Internet, usare il [comando Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e impostare il `--routing-choice` parametro su `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Sostituire il `<resource-group-name>` valore segnaposto con il nome del gruppo di risorse che contiene l'account di archiviazione.

   Sostituire il `<storage-account-name>` valore segnaposto con il nome dell'account di archiviazione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Accedere alla sottoscrizione di Azure.

   - Per avviare Azure Cloud Shell, accedere al portale di Azure [.](https://portal.azure.com)

   - Per accedere all'installazione locale dell'interfaccia della riga di comando, eseguire il [comando az login:](/cli/azure/reference-index#az_login)

     ```azurecli
     az login
     ```
2. Se la propria identità è associata a più sottoscrizioni, impostare come sottoscrizione attiva quella dell’account di archiviazione destinato a ospitare il sito Web statico.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Per modificare la preferenza di routing in routing Internet, usare [il comando az storage account update](/cli/azure/storage/account#az_storage_account_update) e impostare il parametro su `--routing-choice` `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

---

## <a name="configure-a-route-specific-endpoint"></a>Configurare un endpoint specifico della route

È anche possibile configurare un endpoint specifico della route. Ad esempio, è possibile impostare la preferenza di routing per l'endpoint predefinito sul *routing Internet* e quindi pubblicare un endpoint specifico della route che consente il routing del traffico tra i client su Internet e l'account di archiviazione tramite la rete globale Microsoft.

Questa preferenza influisce solo sull'endpoint specifico della route. Questa preferenza non influisce sulle preferenze di routing predefinite.  

### <a name="portal"></a>[Portale](#tab/azure-portal)

1.  Passare all'account di archiviazione nel portale.

2.  In **Impostazioni** scegliere **Rete.**

3.  Nella scheda **Firewall e reti** virtuali in Pubblica endpoint specifici della **route** scegliere la preferenza di routing dell'endpoint specifico della route e quindi fare clic su **Salva**.

    L'immagine seguente mostra **l'opzione di routing di rete Microsoft** selezionata.

    > [!div class="mx-imgBorder"]
    > ![Opzione di routing di rete Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Per configurare un endpoint specifico della route, usare il [comando Set-AzStorageAccount.](/powershell/module/az.storage/set-azstorageaccount) 

   - Per creare un endpoint specifico della route che usa la preferenza di routing di rete Microsoft, impostare il `-PublishMicrosoftEndpoint` parametro su `true` . 

   - Per creare un endpoint specifico della route che usa la preferenza di routing Internet, impostare il `-PublishInternetEndpointTo` parametro su `true` .  

   L'esempio seguente crea un endpoint specifico della route che usa la preferenza di routing di rete Microsoft.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Sostituire il `<resource-group-name>` valore segnaposto con il nome del gruppo di risorse che contiene l'account di archiviazione.

   Sostituire il `<storage-account-name>` valore segnaposto con il nome dell'account di archiviazione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Per configurare un endpoint specifico della route, usare il [comando az storage account update.](/azure/storage/account#az_storage_account_update) 

   - Per creare un endpoint specifico della route che usa la preferenza di routing di rete Microsoft, impostare il `--publish-microsoft-endpoints` parametro su `true` . 

   - Per creare un endpoint specifico della route che usa la preferenza di routing Internet, impostare il `--publish-internet-endpoints` parametro su `true` .  

   Nell'esempio seguente viene creato un endpoint specifico della route che usa la preferenza di routing di rete Microsoft.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Sostituire il `<storage-account-name>` valore segnaposto con il nome dell'account di archiviazione.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Trovare il nome dell'endpoint per un endpoint specifico della route

Se è stato configurato un endpoint specifico della route, è possibile trovare l'endpoint nelle proprietà dell'account di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1.  In **Impostazioni** scegliere **Proprietà**.

    > [!div class="mx-imgBorder"]
    > ![opzione di menu properties](./media/configure-network-routing-preference/properties.png)

2.  **L'endpoint di routing di rete Microsoft** viene visualizzato per ogni servizio che supporta le preferenze di routing. Questa immagine mostra l'endpoint per i servizi BLOB e file.

    > [!div class="mx-imgBorder"]
    > ![Opzione di routing di rete Microsoft per endpoint specifici della route](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Per stampare gli endpoint nella console, usare la `PrimaryEndpoints` proprietà dell'oggetto account di archiviazione.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Sostituire il `<resource-group-name>` valore segnaposto con il nome del gruppo di risorse che contiene l'account di archiviazione.

   Sostituire il `<storage-account-name>` valore segnaposto con il nome dell'account di archiviazione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Per stampare gli endpoint nella console, usare la [proprietà az storage account show](/cli/azure/storage/account#az_storage_account_show) dell'oggetto account di archiviazione.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Sostituire il `<resource-group-name>` valore segnaposto con il nome del gruppo di risorse che contiene l'account di archiviazione.

   Sostituire il `<storage-account-name>` valore segnaposto con il nome dell'account di archiviazione.

---

## <a name="see-also"></a>Vedi anche

- [Preferenza di routing di rete](network-routing-preference.md)
- [Configurare i firewall e le reti virtuali di Archiviazione di Azure](storage-network-security.md)
- [Raccomandazioni sulla sicurezza per archiviazione BLOB](../blobs/security-recommendations.md)
