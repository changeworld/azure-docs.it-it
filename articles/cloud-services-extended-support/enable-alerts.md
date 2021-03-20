---
title: Abilitare il monitoraggio nei servizi cloud (supporto esteso) usando il portale di Azure
description: Abilitare il monitoraggio per le istanze dei servizi cloud (supporto esteso) usando il portale di Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744600"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Abilitare il monitoraggio per i servizi cloud (supporto esteso) usando il portale di Azure

Questo articolo illustra come abilitare gli avvisi per le distribuzioni del servizio cloud (supporto esteso) esistenti. 

## <a name="add-monitoring-rules"></a>Aggiungere regole di monitoraggio
1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare la distribuzione del servizio cloud (supporto esteso) per cui si desidera abilitare gli avvisi. 
3. Selezionare il pannello **avvisi** . 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Immagine mostra la selezione della scheda avvisi nell'portale di Azure.":::

4. Selezionare l'icona **nuovo avviso** .
     :::image type="content" source="media/enable-alerts-2.png" alt-text="Immagine mostra la selezione dell'opzione Aggiungi nuovo avviso.":::

5. Immettere le condizioni desiderate e le azioni necessarie in base alle metriche di interesse per il rilevamento. È possibile definire le regole in base alle singole metriche o al log attività. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="Image Mostra la posizione in cui aggiungere condizioni agli avvisi.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="Immagine che mostra la configurazione della logica del segnale.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="Image Mostra la configurazione della logica del gruppo di azioni.":::

6. Al termine della configurazione degli avvisi, salvare le modifiche e, in base alle metriche configurate, si inizierà a visualizzare il pannello **avvisi** nel tempo.

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).