---
title: Migliorare la risoluzione dei nomi di porte e VLAN
description: Personalizzare i nomi di porta e VLAN sui sensori per arricchire la risoluzione del dispositivo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c976671bccb420ae24d8def7a6574098d86ce6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98803584"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>Migliorare la risoluzione dei nomi di porte e VLAN

È possibile personalizzare i nomi di porta e VLAN sui sensori per arricchire la risoluzione del dispositivo.

## <a name="customize-port-names"></a>Personalizzare i nomi di porta

Azure Defender per le cose assegna automaticamente i nomi alla maggior parte delle porte riservate universalmente, ad esempio DHCP o HTTP. È possibile personalizzare i nomi di porta per le altre porte individuate da Defender for Internet. Ad esempio, assegnare un nome a una porta non riservata perché la porta Mostra un'attività insolitamente elevata.

Questi nomi vengono visualizzati nei casi seguenti:

  - È possibile selezionare i **gruppi di dispositivi** dalla mappa del dispositivo.

  - È possibile creare widget che forniscono informazioni sulla porta.

### <a name="view-custom-port-names-in-the-device-map"></a>Visualizzare i nomi di porta personalizzati nella mappa dei dispositivi

Le porte che includono un nome definito dagli utenti vengono visualizzate nella mappa del dispositivo, nel gruppo **applicazioni note** .

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Screenshot della mappa del dispositivo, che mostra il gruppo di applicazioni noto.":::

### <a name="view-custom-port-names-in-widgets"></a>Visualizzare i nomi di porta personalizzati nei widget

I nomi di porta definiti vengono visualizzati nei widget che coprono il traffico in base alla porta.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Coprire il traffico.":::

Per definire nomi di porta personalizzati:

1. Selezionare **impostazioni di sistema** , quindi fare clic su **alias standard**.

2. Selezionare **Add Port alias**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Aggiungere un alias di porta.":::

3. Immettere il numero di porta, selezionare **TCP/UDP** oppure selezionare **entrambi** e aggiungere il nome.

4. Selezionare **Salva**.

## <a name="configure-vlan-names"></a>Configurare i nomi VLAN

È possibile arricchire i dati di inventario dei dispositivi con i tag e i numeri VLAN del dispositivo. Oltre all'arricchimento dei dati, è possibile visualizzare il numero di dispositivi per VLAN e visualizzare la larghezza di banda in base ai widget VLAN.

Il supporto VLAN è basato su 802.1 q (fino all'ID VLAN 4094).

Per il recupero delle informazioni VLAN sono disponibili due metodi:

- **Individuato automaticamente**: per impostazione predefinita, il sensore individua automaticamente le VLAN. Le VLAN rilevate con traffico vengono visualizzate nella schermata di configurazione della VLAN, nei report di data mining e in altri report contenenti informazioni VLAN. Le VLAN inutilizzate non vengono visualizzate. Non è possibile modificare o eliminare queste VLAN. 

  È necessario aggiungere un nome univoco a ogni VLAN. Se non si aggiunge un nome, il numero di VLAN viene visualizzato in tutte le posizioni in cui viene segnalata la VLAN.

- **Aggiunta manuale**: è possibile aggiungere VLAN manualmente. È necessario aggiungere un nome univoco per ogni VLAN aggiunta manualmente ed è possibile modificare o eliminare tali VLAN.

I nomi VLAN possono contenere fino a 50 caratteri ASCII.

> [!NOTE]
> I nomi VLAN non sono sincronizzati tra il sensore e la console di gestione. È necessario definire anche il nome nella console di gestione.  
Per i commutatori Cisco, aggiungere la riga seguente alla configurazione span: `monitor session 1 destination interface XX/XX encapsulation dot1q` . In tale comando *XX/XX* è il nome e il numero della porta.

Per configurare le VLAN:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nella finestra **Impostazioni sistema** selezionare **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Usare le impostazioni di sistema per modificare le VLAN.":::

3. Aggiungere un nome univoco accanto a ogni ID VLAN.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le informazioni sul dispositivo arricchito in diversi report:

- [Esaminare i rilevamenti dei sensori in un inventario dei dispositivi](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Tendenze dei sensori e report statistici](how-to-create-trends-and-statistics-reports.md)
- [Query data mining sensori](how-to-create-data-mining-queries.md)
