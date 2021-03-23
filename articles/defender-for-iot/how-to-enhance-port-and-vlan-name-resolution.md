---
title: Migliorare la risoluzione dei nomi di porte e VLAN
description: Personalizzare i nomi di porta e VLAN sui sensori per arricchire la risoluzione del dispositivo.
ms.date: 12/13/2020
ms.topic: how-to
ms.openlocfilehash: de6fbe70d5a5359ad4e4c276642b9b9ed0cef00f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784170"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>Miglioramento della porta, della VLAN e della risoluzione del sistema operativo

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

Per configurare i nomi VLAN:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nella finestra **Impostazioni sistema** selezionare **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Usare le impostazioni di sistema per modificare le VLAN.":::

3. Aggiungere un nome univoco accanto a ogni ID VLAN.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>Migliorare la classificazione del sistema operativo del dispositivo: miglioramento dei dati

I sensori individuano continuamente i nuovi dispositivi, nonché le modifiche apportate ai dispositivi individuati in precedenza, inclusi i tipi di sistema operativo.

In determinate circostanze, i conflitti possono essere rilevati nei sistemi operativi individuati. Questo problema può verificarsi, ad esempio, se si dispone di una versione del sistema operativo che fa riferimento a sistemi desktop o server. In tal caso, si riceverà una notifica con le classificazioni dei sistemi operativi facoltativi.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Migliorare i dati.":::

Esaminare le raccomandazioni per migliorare la classificazione del sistema operativo. Questa classificazione viene visualizzata nell'inventario dei dispositivi, nei report di data mining e in altre visualizzazioni. Verificare che queste informazioni siano aggiornate possono migliorare l'accuratezza di avvisi, minacce e report di analisi dei rischi.

Per accedere alle raccomandazioni del sistema operativo:

1. Selezionare **impostazioni di sistema**.
1. Selezionare **miglioramento dei dati**.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le informazioni sul dispositivo arricchito in diversi report:

- [Esaminare i rilevamenti dei sensori in un inventario dei dispositivi](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Tendenze dei sensori e report statistici](how-to-create-trends-and-statistics-reports.md)
- [Query data mining sensori](how-to-create-data-mining-queries.md)
