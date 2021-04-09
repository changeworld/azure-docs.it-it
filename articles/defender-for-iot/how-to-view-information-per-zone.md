---
title: Informazioni sui dispositivi in zone specifiche
description: Usare la console di gestione locale per ottenere informazioni complete sulla visualizzazione per zona specifica
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776225"
---
# <a name="view-information-per-zone"></a>Visualizza informazioni per area


## <a name="view-a-device-map-for-a-zone"></a>Visualizzare una mappa del dispositivo per una zona

Visualizzare una mappa del dispositivo per una zona selezionata in un sensore. Questa vista consente di visualizzare tutti gli elementi di rete correlati alla zona selezionata, inclusi i sensori, i dispositivi connessi e altre informazioni.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Screenshot della mappa della zona.":::


- Nella finestra di **gestione del sito** selezionare **Visualizza mapping zone** dalla barra che contiene il nome della zona.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Area predefinita per business unit predefinito.":::

Viene visualizzata la finestra **mappa del dispositivo** . Sono disponibili gli strumenti seguenti per la visualizzazione di dispositivi e informazioni sul dispositivo dalla mappa. Per informazioni dettagliate su ognuna di queste funzionalità, vedere la *Guida dell'utente della piattaforma Defender for* Internet.

- **Viste Zoom mappa**: visualizzazione semplificata, visualizzazione connessioni e visualizzazione dettagliata. La visualizzazione mappa visualizzata varia a seconda del livello di zoom della mappa. Per passare da una vista mappa a un'altra, modificare i livelli di zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Mappa di ricerca e strumenti di layout**: strumenti usati per visualizzare vari segmenti di rete, dispositivi, gruppi di dispositivi o livelli.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Screenshot della visualizzazione degli strumenti di layout e di ricerca.":::

- **Etichette e indicatori nei dispositivi:** Ad esempio, il numero di dispositivi raggruppati in una subnet in una rete IT. In questo esempio è 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Screenshot di etichette e indicatori.":::

- **Visualizzare le proprietà** del dispositivo: ad esempio, il sensore che monitora il dispositivo e le proprietà di base del dispositivo. Fare clic con il pulsante destro del mouse sul dispositivo per visualizzare le proprietà del dispositivo.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Screenshot della visualizzazione delle proprietà del dispositivo.":::

- **Avviso associato a un dispositivo:** Fare clic con il pulsante destro del mouse sul dispositivo per visualizzare gli avvisi correlati.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Screenshot della vista Mostra avvisi.":::

## <a name="view-alerts-associated-with-a-zone"></a>Visualizzare gli avvisi associati a una zona

Per visualizzare gli avvisi associati a una zona specifica:

- Selezionare l'icona di avviso sotto forma di finestra della **zona** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Visualizzazione business unit predefinita con esempi.":::

Per altre informazioni, vedere [Panoramica: uso degli avvisi](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Visualizzare l'inventario dei dispositivi di una zona

Per visualizzare l'inventario dei dispositivi associato a una zona specifica:

- Selezionare **Visualizza inventario dispositivi** nella finestra **zona** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Verrà visualizzata la schermata inventario dispositivo.":::

Per altre informazioni, vedere [esaminare tutti i rilevamenti dei sensori aziendali nell'inventario di un dispositivo](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Visualizzare informazioni sulla zona aggiuntive

Sono disponibili le seguenti informazioni aggiuntive sulla zona:

- **Dettagli zona**: visualizzare il numero di dispositivi, avvisi e sensori associati alla zona.

- **Dettagli del sensore**: consente di visualizzare il nome, l'indirizzo IP e la versione di ogni sensore assegnato alla zona.

- **Stato connettività**: se un sensore è disconnesso, connettersi dal sensore. Vedere [connettere i sensori alla console di gestione locale](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- Stato **aggiornamento**: se il sensore connesso viene aggiornato, verranno visualizzati gli Stati di aggiornamento. Durante l'aggiornamento, la console di gestione locale non riceve le informazioni sul dispositivo dal sensore.

## <a name="next-steps"></a>Passaggi successivi

[Acquisire informazioni su minacce locali, globali e a livello di area](how-to-gain-insight-into-global-regional-and-local-threats.md)
