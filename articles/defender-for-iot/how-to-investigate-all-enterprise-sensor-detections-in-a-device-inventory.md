---
title: Informazioni sui dispositivi individuati da tutti i sensori aziendali
description: Usare l'inventario dei dispositivi nella console di gestione locale per ottenere una visualizzazione completa delle informazioni sui dispositivi dai sensori connessi. Usare gli strumenti di importazione, esportazione e filtro per gestire queste informazioni.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 47e3833fe9f35467c1d16945a9b336d4014b2efd
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839773"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Esaminare tutti i rilevamenti dei sensori aziendali nell'inventario del dispositivo

È possibile visualizzare le informazioni sui dispositivi dai sensori connessi usando l' *inventario dei dispositivi* nella console di gestione locale. Questa funzionalità offre una panoramica completa di tutte le informazioni sulla rete. Usare gli strumenti di importazione, esportazione e filtro per gestire queste informazioni. Vengono visualizzate anche le informazioni sullo stato relative alle versioni dei sensori connessi.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Screenshot della tabella dei dati di inventario del dispositivo.":::

Nella tabella seguente vengono descritte le colonne della tabella nell'inventario dei dispositivi.

| Parametro | Descrizione |
|--|--|
| **Avvisi non riconosciuti** | Numero di avvisi non gestiti associati a questo dispositivo. |
| **Business Unit** | Business Unit che contiene questo dispositivo. |
| **Area** | Area che contiene questo dispositivo. |
| **Sito** | Sito che contiene questo dispositivo. |
| **Zona** | Area che contiene questo dispositivo. |
| **Appliance** | Il sensore Azure Defender per l'IT che protegge questo dispositivo. |
| **Nome** | Il nome del dispositivo come Defender per l'it è stato individuato. |
| **Tipo** | Tipo di dispositivo, ad esempio PLC o HMI. |
| **Fornitore** | Nome del fornitore del dispositivo, come definito nell'indirizzo MAC. |
| **Sistema operativo** | Sistema operativo del dispositivo. |
| **Firmware** | Il firmware del dispositivo. |
| **Indirizzo IP** | Indirizzo IP del dispositivo. |
| **VLAN** | VLAN del dispositivo. |
| **Indirizzo MAC** | Indirizzo MAC del dispositivo. |
| **Protocolli** | Protocolli usati dal dispositivo. |
| **Avvisi non riconosciuti** | Numero di avvisi non gestiti associati a questo dispositivo. |
| **Autorizzato** | Stato di autorizzazione del dispositivo:<br />- **True**: il dispositivo è stato autorizzato.<br />- **False**: il dispositivo non è stato autorizzato. |
| **È noto come scanner** | Indica se il dispositivo esegue attività di tipo analisi nella rete. |
| **È un dispositivo di programmazione** | Indica se si tratta di un dispositivo di programmazione:<br />- **True**: il dispositivo esegue attività di programmazione per PLC, RTU e controller, rilevanti per le stazioni di progettazione.<br />- **False**: il dispositivo non è un dispositivo di programmazione. |
| **Gruppi** | Gruppi a cui partecipa questo dispositivo. |
| **Ultima attività** | Ultima attività eseguita dal dispositivo. |
| **Discovered** | Quando il dispositivo è stato visualizzato per la prima volta nella rete. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrare i dati nell'inventario dei dispositivi aziendali

Le funzionalità di integrazione dei dati consentono di migliorare i dati nell'inventario dei dispositivi con le informazioni provenienti da altre risorse aziendali. Queste origini includono CMDBs, DNS, firewall e API Web.

È possibile usare queste informazioni per apprendere. Ad esempio:

- Date di acquisto del dispositivo e date di fine della garanzia

- Utenti responsabili di ogni dispositivo

- Ticket aperti per i dispositivi

- Ultima data di aggiornamento del firmware

- Dispositivi autorizzati ad accedere a Internet

- Dispositivi che eseguono applicazioni antivirus attive

- Utenti connessi ai dispositivi

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Tabella di dati nella schermata inventario asset.":::

È possibile integrare i dati in uno dei seguenti casi:

- Aggiunta manuale

- Esecuzione di script personalizzati forniti da Defender for Internet

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagramma di Enterprise Data Integrator.":::

Per configurare il sistema in modo da ricevere query API Web, è possibile collaborare con Defender per il supporto tecnico.

Per aggiungere manualmente i dati:

1. Nel menu laterale selezionare **inventario dispositivi** e quindi selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Modificare le impostazioni di inventario del dispositivo.":::

2. Nella finestra di dialogo **Impostazioni inventario dispositivi** selezionare **Aggiungi colonna personalizzata**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Aggiungere una colonna personalizzata all'inventario.":::

3. Nella finestra di dialogo **Aggiungi colonna personalizzata** aggiungere il nome della nuova colonna (fino a 250 caratteri UTF), selezionare **manuale** e selezionare **Salva**. Il nuovo elemento verrà visualizzato nella finestra di dialogo **Impostazioni inventario dispositivi** .

4. Nell'angolo in alto a destra della finestra **inventario dispositivo** selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: e selezionare **Esporta tutti i dispositivi inventario**. Viene generato il file CSV.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="File CSV esportato.":::

5. Aggiungere manualmente le informazioni alla nuova colonna e salvare il file.

6. Nell'angolo in alto a destra della finestra **inventario dispositivo** selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: , selezionare **Importa colonne di input manuali** e passare al file CSV. I nuovi dati vengono visualizzati nella tabella **inventario dispositivo** .

Per integrare i dati di altre entità aziendali:

1. Nell'angolo in alto a destra della finestra **inventario dispositivo** selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: e selezionare **Esporta tutti i dispositivi inventario**.

2. Nella finestra di dialogo **Impostazioni inventario dispositivi** selezionare **Aggiungi colonna personalizzata**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Aggiungere una colonna personalizzata all'inventario.":::

3. Nella finestra di dialogo **Aggiungi colonna personalizzata** aggiungere il nome della nuova colonna (fino a 250 caratteri UTF), quindi selezionare **automatico**. Verranno visualizzate le opzioni **carica script** e **script di test** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Aggiungere automaticamente colonne personalizzate.":::

4. Caricare e testare lo script ricevuto da [supporto tecnico Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="retrieve-information-from-the-device-inventory"></a>Recuperare le informazioni dall'inventario dei dispositivi

È possibile recuperare una vasta gamma di informazioni sul dispositivo rilevate dai sensori gestiti e integrare tali informazioni con i sistemi partner. Ad esempio, è possibile recuperare il sensore, la zona, l'ID sito, l'indirizzo IP, l'indirizzo MAC, il firmware, il protocollo e le informazioni sul fornitore. Filtrare le informazioni recuperate in base a:

- Dispositivi autorizzati e non autorizzati.

- Dispositivi associati a siti specifici.

- Dispositivi associati a zone specifiche.

- Dispositivi associati a sensori specifici.

Per recuperare e integrare queste informazioni, usare Defender per i comandi dell'API. Per altre informazioni, vedere [Defender per le API del sensore API e della console di gestione](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtrare l'inventario dei dispositivi

È possibile filtrare l'inventario dei dispositivi per visualizzare le colonne di interesse. Ad esempio, è possibile visualizzare le informazioni sul dispositivo PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Screenshot dell'inventario degli asset.":::

Il filtro viene cancellato quando si esce dalla finestra.

Per utilizzare lo stesso filtro più volte, è possibile salvare un filtro o una combinazione di filtri necessari. È possibile aprire un riquadro sinistro e visualizzare i filtri salvati:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Schermata inventario asset.":::

Per filtrare l'inventario dei dispositivi:

1. Nella colonna che si desidera filtrare selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. Nella finestra di dialogo **filtro** selezionare il tipo di filtro:

   - **Equals**: valore esatto in base al quale si desidera filtrare la colonna. Se ad esempio si filtra la colonna protocollo in base a **uguale** a e `value=ICMP` , nella colonna vengono visualizzati solo i dispositivi che utilizzano il protocollo ICMP.

   - **Contains**: valore contenuto tra gli altri valori nella colonna. Se ad esempio si filtra la colonna protocollo in base a **Contains** e `value=ICMP` , nella colonna vengono presentati i dispositivi che usano il protocollo ICMP come parte dell'elenco di protocolli utilizzati dal dispositivo.

3. Per organizzare le informazioni di colonna in base all'ordine alfabetico, selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Organizzare l'ordine selezionando le :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: frecce e.

4. Per salvare un nuovo filtro, definire il filtro e selezionare **Salva con nome**.

5. Per modificare le definizioni di filtro, modificare le definizioni e selezionare **Salva modifiche**.

## <a name="view-device-information-per-zone"></a>Visualizza le informazioni sul dispositivo per area

È possibile apprendere le informazioni seguenti sui dispositivi in una zona.

### <a name="view-a-device-map"></a>Visualizzare una mappa del dispositivo

Per visualizzare una mappa del dispositivo del sensore per una zona selezionata:

- Nella finestra di **gestione del sito** selezionare **Visualizza mapping zone** dalla barra che contiene il nome della zona.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Area predefinita per business unit predefinito.":::

Viene visualizzata la finestra **mappa del dispositivo** . Mostra tutti gli elementi di rete correlati alla zona selezionata, inclusi i sensori, i dispositivi connessi e altre informazioni.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Screenshot della mappa della zona.":::

Sono disponibili gli strumenti seguenti per la visualizzazione di dispositivi e informazioni sul dispositivo dalla mappa. Per informazioni dettagliate su ognuna di queste funzionalità, vedere la *Guida dell'utente della piattaforma Defender for* Internet.

- **Viste Zoom mappa**: visualizzazione semplificata, visualizzazione connessioni e visualizzazione dettagliata. La visualizzazione mappa visualizzata varia a seconda del livello di zoom della mappa. Per passare da una vista mappa a un'altra, modificare i livelli di zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Mappa di ricerca e strumenti di layout**: strumenti usati per visualizzare vari segmenti di rete, dispositivi, gruppi di dispositivi o livelli.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Screenshot della visualizzazione degli strumenti di layout e di ricerca.":::

- **Etichette e indicatori nei dispositivi:** Ad esempio, il numero di dispositivi raggruppati in una subnet in una rete IT. In questo esempio è 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Screenshot di etichette e indicatori.":::

- **Visualizzare le proprietà** del dispositivo: ad esempio, il sensore che monitora il dispositivo e le proprietà di base del dispositivo. Fare clic con il pulsante destro del mouse sul dispositivo per visualizzare le proprietà del dispositivo.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Screenshot della visualizzazione delle proprietà dell'asset.":::

- **Avviso associato a un dispositivo:** Fare clic con il pulsante destro del mouse sul dispositivo per visualizzare gli avvisi correlati.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Screenshot della vista Mostra avvisi.":::

### <a name="view-alerts-associated-with-a-zone"></a>Visualizzare gli avvisi associati a una zona

Per visualizzare gli avvisi associati a una zona specifica:

- Selezionare l'icona di avviso sotto forma di finestra della **zona** . 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Visualizzazione business unit predefinita con esempi.":::

Per altre informazioni, vedere [Panoramica: uso degli avvisi](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Visualizzare l'inventario dei dispositivi di una zona

Per visualizzare l'inventario dei dispositivi associato a una zona specifica:

- Selezionare **Visualizza inventario dispositivi** nella finestra **zona** .

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Verrà visualizzata la schermata inventario dispositivo.":::

Per altre informazioni, vedere [esaminare tutti i rilevamenti dei sensori aziendali nell'inventario di un dispositivo](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

### <a name="view-additional-zone-information"></a>Visualizzare informazioni sulla zona aggiuntive

Sono disponibili le seguenti informazioni aggiuntive sulla zona:

- **Dettagli zona**: visualizzare il numero di dispositivi, avvisi e sensori associati alla zona.

- **Dettagli del sensore**: consente di visualizzare il nome, l'indirizzo IP e la versione di ogni sensore assegnato alla zona.

- **Stato connettività**: se un sensore è disconnesso, connettersi dal sensore. Vedere [connettere i sensori alla console di gestione locale](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- Stato **aggiornamento**: se il sensore connesso viene aggiornato, verranno visualizzati gli Stati di aggiornamento. Durante l'aggiornamento, la console di gestione locale non riceve le informazioni sul dispositivo dal sensore.

## <a name="see-also"></a>Vedere anche

[Esaminare i rilevamenti dei sensori nell'inventario dei dispositivi](how-to-investigate-sensor-detections-in-a-device-inventory.md)
