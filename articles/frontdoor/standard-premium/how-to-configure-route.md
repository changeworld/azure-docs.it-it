---
title: Configurare il percorso di Azure front door
description: Questo articolo illustra come configurare una route tra i domini e i gruppi di origine.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099535"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Configurare una route standard/Premium di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questo articolo illustra tutte le impostazioni usate per la creazione di una route di Azure front door (AFD) per un endpoint esistente. Dopo aver aggiunto un dominio personalizzato e un'origine all'endpoint di Azure front door esistente, è necessario configurare la route per definire l'associazione tra domini e origini per indirizzare il traffico tra di essi.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Prima di poter configurare un route di Azure front door, è necessario avere creato almeno un gruppo di origine e un dominio personalizzato all'interno dell'endpoint corrente. 

Per configurare un gruppo di origine, vedere [creare un nuovo gruppo di origine standard/Premium di Azure front door](how-to-create-origin.md). 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Creare una nuova route standard/Premium di Azure front door

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al profilo standard/Premium di Azure front door.

1. Selezionare **Gestione endpoint** in **Impostazioni**.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Screenshot delle impostazioni di Gestione endpoint di front-end." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Quindi selezionare **Modifica endpoint** per l'endpoint per cui si vuole configurare la route.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Screenshot della selezione dell'endpoint di modifica.":::

1. Verrà visualizzata la pagina **Modifica endpoint** . Selezionare **+ Aggiungi** per le route.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Screenshot della pagina Aggiungi una route nella pagina di modifica dell'endpoint.":::    
    
1. Nella pagina **Aggiungi route** immettere o selezionare le informazioni seguenti.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Screenshot della pagina Aggiungi una route." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Impostazione | Valore |
    | --- | --- |
    | Nome | Immettere un nome univoco per la nuova route. |   
    | Dominio| Selezionare uno o più domini che sono stati convalidati e che non sono associati a un'altra route |
    | Criteri di corrispondenza  | Configurare tutti i modelli di percorso URL che verrà accettato da questa route. Ad esempio, è possibile impostare questa impostazione su `/images/*` per accettare tutte le richieste sull'URL `www.contoso.com/images/*` . AFD tenterà di determinare il traffico in base alla corrispondenza esatta prima, se non sono presenti percorsi di corrispondenza esatti, quindi cercare un percorso con caratteri jolly corrispondente a. Se non viene trovata alcuna regola di routing con un percorso corrispondente, rifiutare la richiesta e restituire una risposta HTTP di errore 400 - Richiesta non valida. |
    | Protocolli accettati | Specificare i protocolli che devono essere accettati da Azure front door quando il client sta effettuando la richiesta. |
    | reindirizzamento | Specificare se HTTPS viene applicato per la richiesta in ingresso con una richiesta HTTP |
    | Gruppo di origine | Consente di selezionare il gruppo di origine da trasmettere quando si verifica la richiesta di ritorno all'origine. |
    | Percorso di origine | Immettere il percorso delle risorse che si desidera memorizzare nella cache. Per consentire la memorizzazione nella cache di qualsiasi risorsa nel dominio, lasciare vuota questa impostazione. |
    | Protocollo di inoltro | Selezionare il protocollo usato per l'invio della richiesta. |
    | Memorizzazione nella cache | Selezionare questa opzione per abilitare la memorizzazione nella cache del contenuto statico con lo sportello anteriore di Azure. |
    | Regola | Selezionare i set di regole che verranno applicati a questa route. Per altre informazioni su come configurare le regole, vedere [configurare un set di regole per il front-end di Azure](how-to-configure-rule-set.md) | 

1. Selezionare **Aggiungi** per creare la nuova route. La route verrà visualizzata nell'elenco delle route per l'endpoint.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Screenshot dell'elenco di route.":::  
    
## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare una route quando non è più necessaria, selezionare la route e quindi selezionare **Elimina**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Screenshot dell'eliminazione di una route.":::  

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sui domini personalizzati, continuare con l'esercitazione per aggiungere un dominio personalizzato all'endpoint di Azure front door.

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato]()
