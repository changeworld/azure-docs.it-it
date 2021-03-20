---
title: 'Sportello anteriore di Azure: configurare il set di regole della porta anteriore'
description: Questo articolo fornisce indicazioni su come configurare un set di regole.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715599"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Configurare un set di regole con Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Questo articolo illustra come creare un set di regole e il primo set di regole nel portale di Azure. Si apprenderà quindi come associare il set di regole a una route dalla pagina del set di regole o da Gestione endpoint.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

* Prima di poter configurare un set di regole, è necessario creare prima di tutto uno standard/Premium di Azure front door. Per altre informazioni, vedere [Guida introduttiva: creare un profilo standard o Premium di Azure front door](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Configurare il set di regole in portale di Azure

1. Nel profilo della porta anteriore selezionare **set di regole** situato in **Impostazioni**. Selezionare **Aggiungi** e assegnare un nome al set di regole.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Screenshot della pagina di destinazione del set di regole.":::
    
1. Selezionare **Aggiungi regola** per creare la prima regola. Assegnare un nome alla regola. Selezionare quindi **Aggiungi condizione** o **Aggiungi azione** per definire la regola. È possibile aggiungere fino a 10 condizioni e 5 azioni per una regola. In questo esempio viene usata la variabile server per aggiungere un'intestazione di risposta 8Geo-Country * per le richieste che includono *Contoso* nell'URL.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Screenshot della pagina di configurazione del set di regole.":::
    
    > [!NOTE]
    > * Per eliminare una condizione o un'azione da una regola, è possibile usare il Cestino sul lato destro della condizione o dell'azione specifica.
    > * Per creare una regola che si applica a tutto il traffico in ingresso, non specificare alcuna condizione.
    > * Per arrestare la valutazione delle regole rimanenti se viene soddisfatta una regola specifica, selezionare **Interrompi valutazione della regola rimanente**. Se questa opzione è selezionata e tutte le regole rimanenti nel set di regole non verranno eseguite indipendentemente dal fatto che siano state soddisfatte le condizioni di corrispondenza.  

1. Per determinare la priorità delle regole all'interno del set di regole, è possibile utilizzare i pulsanti freccia per spostare le regole in un livello superiore o inferiore. L'elenco è in ordine crescente, quindi la regola più importante è elencata per prima.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Screenshot della priorità del set di regole." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Dopo aver creato una o più regole, selezionare **Salva** per completare la creazione del set di regole.

1. A questo punto, associare il set di regole a una route in modo che possa essere applicato. È possibile associare le regole impostate tramite la pagina del set di regole oppure è possibile passare a Gestione endpoint per creare l'associazione.
 
    **Pagina set di regole**: 
    
    1. Consente di selezionare il set di regole da associare.
    
    1. Selezionare il collegamento non *associato* .
     

    1. Nella pagina **associa una route** selezionare quindi l'endpoint e la route che si desidera associare al set di regole. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Screenshot della pagina creare una route.":::    
        
    1. Selezionare *Avanti* per modificare gli ordini del set di regole se sono presenti più set di regole nella route selezionata. Il set di regole verrà eseguito dall'alto verso il basso. È possibile modificare gli ordini selezionando il set di regole e spostarlo verso l'alto o verso il basso. Quindi selezionare *associa*.
    
        > [!Note]
        > In questa pagina è possibile associare solo un set di regole a una singola route. Per associare un set di regole a più route, utilizzare Gestione endpoint.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Screenshot degli ordini del set di regole.":::
    
    1. Il set di regole è ora associato a una route. È possibile esaminare l'intestazione della risposta e verificare che sia stato aggiunto il paese geografico.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Screenshot della regola associata a una route.":::

   **Gestione endpoint**: 
    
    1. Passare a Gestione endpoint, selezionare l'endpoint che si desidera associare al set di regole.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Screenshot della selezione dell'endpoint in Gestione endpoint." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Selezionare *Modifica endpoint*.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Screenshot della selezione di modifica endpoint in Gestione endpoint." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Selezionare la route. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Screenshot della selezione di una route.":::
    
    1. Nella pagina *Aggiorna route* , in *regole*, selezionare i set di regole che si desidera associare alla route dall'elenco a discesa. È quindi possibile modificare gli ordini spostando set di regole verso l'alto e verso il basso. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Screenshot della pagina Aggiorna una route.":::
    
    1. Quindi selezionare *Aggiorna* o *Aggiungi* per completare l'associazione.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Eliminare un set di regole dal profilo di Azure front door

Nei passaggi precedenti è stato configurato e associato un set di regole alla route. Se non si desidera più che il set di regole associato alla porta anteriore, è possibile rimuovere il set di regole completando i passaggi seguenti:

1. Passare alla **pagina set di regole** in **Impostazioni** per annullare l'associazione del set di regole da tutte le route associate.

1. Espandere la route e selezionare i tre puntini di sospensione. Selezionare quindi *modifica Route*.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Screenshot della route espansa nel set di regole.":::

1. Passare alla sezione regole nella pagina Route, selezionare il set di regole e selezionare il pulsante *Elimina* . 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Screenshot della pagina Aggiorna route per eliminare un set di regole." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Selezionare *Aggiorna* e il set di regole dissocierà dalla route.

1. Ripetere i passaggi 2-5 per annullare l'associazione di altre route associate a questo set di regole fino a quando non viene visualizzato lo stato delle route non *associato*.

1. Per il set di regole non *associato* è possibile eliminare il set di regole facendo clic sui tre puntini a destra e selezionare *Elimina*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Screenshot dell'eliminazione di un set di regole.":::

1. Il set di regole ora è stato eliminato.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere [intestazioni di sicurezza con regole impostate](how-to-add-security-headers.md).
