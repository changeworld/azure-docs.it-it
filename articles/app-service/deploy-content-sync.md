---
title: Sincronizzare il contenuto da una cartella Cloud
description: Informazioni su come distribuire l'app nel servizio app Azure tramite la sincronizzazione del contenuto da una cartella Cloud, tra cui OneDrive o Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051233"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizzare i contenuti da una cartella nel cloud per il servizio app di Azure
Questo articolo illustra come eseguire la sincronizzazione del contenuto in [Servizio app di Azure](./overview.md) da Dropbox e OneDrive. 

Con l'approccio di sincronizzazione del contenuto, è possibile usare il codice e il contenuto dell'app in una cartella Cloud designata per assicurarsi che si trovi in uno stato pronto per la distribuzione e quindi sincronizzarlo con il servizio app facendo clic su un pulsante. 

A causa delle differenze sottostanti nelle API, **OneDrive for Business** non è attualmente supportato.

> [!NOTE]
> La pagina **centro di sviluppo (classica)** nell'portale di Azure, che è l'esperienza di distribuzione precedente, sarà deprecata a partire da marzo 2021. Questa modifica non avrà alcun effetto sulle impostazioni di distribuzione esistenti nell'app ed è possibile continuare a gestire la distribuzione delle app nella pagina **centro distribuzione** .

## <a name="enable-content-sync-deployment"></a>Abilitare la distribuzione per la sincronizzazione del contenuto

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app del servizio app.

1. Nel menu a sinistra fare clic su impostazioni di **Deployment Center**  >  . 

1. In **origine** selezionare **OneDrive** o **Dropbox**.

1. Fare clic su **autorizza** e seguire le istruzioni di autorizzazione. 

    ![Viene illustrato come autorizzare OneDrive o Dropbox in centro distribuzione nel portale di Azure.](media/app-service-deploy-content-sync/choose-source.png)

    È sufficiente autorizzare con OneDrive o Dropbox una sola volta per l'account Azure. Per autorizzare un account OneDrive o Dropbox diverso per un'app, fare clic su **Cambia account**.

1. In **cartella** selezionare la cartella da sincronizzare. La cartella viene creata nel percorso del contenuto designato seguente in OneDrive o Dropbox. 
   
    * **OneDrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. Fare clic su **Salva**.

## <a name="synchronize-content"></a>Sincronizzare il contenuto

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app del servizio app.

1. Nel menu a sinistra fare clic su **centro distribuzione**  >  **Ridistribuisci/Sincronizza**. 

    ![Mostra come sincronizzare la cartella Cloud con il servizio app.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Fare clic su **OK** per confermare la sincronizzazione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/cli)

Avviare una sincronizzazione eseguendo il comando seguente e sostituendo \<group-name> e \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Avviare una sincronizzazione eseguendo il comando seguente e sostituendo \<group-name> e \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Disabilitare la distribuzione per la sincronizzazione dei contenuti

1. Nella [portale di Azure](https://portal.azure.com)passare alla pagina di gestione dell'app del servizio app.

1. Nel menu a sinistra fare clic su impostazioni **centro distribuzione**  >    >  **Disconnetti**. 

    ![Mostra come disconnettere la sincronizzazione delle cartelle Cloud con l'app del servizio app nel portale di Azure.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire dal repository GIT locale](deploy-local-git.md)