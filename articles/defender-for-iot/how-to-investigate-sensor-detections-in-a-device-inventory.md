---
title: Ottenere informazioni dettagliate sui dispositivi individuati da un sensore specifico
description: L'inventario del dispositivo Visualizza un'ampia gamma di attributi del dispositivo rilevati da un sensore.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 4daec83f44a545d7837a7e73e847f56b1f5770e7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782096"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Esaminare i rilevamenti dei sensori in un inventario dei dispositivi

L'inventario del dispositivo Visualizza un'ampia gamma di attributi del dispositivo rilevati da un sensore. Sono disponibili opzioni per:

 - Filtrare facilmente le informazioni.

 - Esportare le informazioni in un file CSV.

 - Importa i dettagli del registro di sistema di Windows.

 - Creare gruppi da visualizzare nella mappa del dispositivo.

## <a name="view-device-attributes-in-the-device-inventory"></a>Visualizzare gli attributi del dispositivo nell'inventario dei dispositivi

Gli attributi seguenti vengono visualizzati nella tabella inventario dispositivo.

| Parametro | Descrizione |
|--|--|
| Nome | Nome del dispositivo come sensore individuato o immesso dall'utente. |
| Tipo | Tipo di dispositivo determinato dal sensore o immesso dall'utente. |
| Vendor | Nome del fornitore del dispositivo, come definito nell'indirizzo MAC. |
| Sistema operativo | Sistema operativo del dispositivo, se rilevato. |
| Versione del firmware | Il firmware del dispositivo, se rilevato. |
| Indirizzo IP | Indirizzo IP del dispositivo in cui è definito. |
| VLAN | VLAN del dispositivo. Per informazioni dettagliate su come indicare al sensore di individuare le VLAN, vedere [definire i nomi VLAN](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (How-to-define-Management-Console-Network-Settings. MD # define-VLAN-names). |
| Indirizzo MAC | Indirizzo MAC del dispositivo. |
| Protocolli | Protocolli usati dal dispositivo. |
| Avvisi non riconosciuti | Numero di avvisi non riconosciuti associati a questo dispositivo. |
| Autorizzato | Stato di autorizzazione definito dall'utente:<br />- **True**: il dispositivo è stato autorizzato.<br />- **False**: il dispositivo non è stato autorizzato. |
| È noto come scanner | Definito come dispositivo di analisi della rete da parte dell'utente. |
| È un dispositivo di programmazione | Definito come un dispositivo di programmazione autorizzato dall'utente. <br />- **True**: il dispositivo esegue attività di programmazione per PLC, RTU e controller, rilevanti per le stazioni di progettazione. <br />- **False**: il dispositivo non è un dispositivo di programmazione. |
| Gruppi | Gruppi a cui partecipa questo dispositivo. |
| Ultima attività | Ultima attività eseguita dal dispositivo. |
| Discovered | Quando il dispositivo è stato visualizzato per la prima volta nella rete. |

Per visualizzare l'inventario dei dispositivi:

1. Nel riquadro sinistro selezionare **dispositivi**. Si apre il riquadro **dispositivi** a destra.

2. Nel riquadro **dispositivi** selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Per nascondere e visualizzare le colonne, personalizzare la tabella di inventario del dispositivo:

1. Nel menu in alto a destra dell'inventario del dispositivo selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Schermata Impostazioni inventario dispositivo.":::

2. Nella finestra **Impostazioni inventario dispositivi** selezionare le colonne che si desidera visualizzare nella tabella inventario dispositivo.

3. Modificare il percorso delle colonne nella tabella utilizzando le frecce.

4. Selezionare **Salva**. La finestra **Impostazioni inventario dispositivi** si chiude e le nuove impostazioni vengono visualizzate nella tabella.

### <a name="create-temporary-device-inventory-filters"></a>Creare filtri di inventario dispositivi temporanei

È possibile impostare un filtro che definisce le informazioni visualizzate nella tabella. Ad esempio, è possibile decidere di visualizzare solo le informazioni del dispositivo PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Apprendimento dei dispositivi.":::

Il filtro non viene salvato quando si lascia l'inventario.

### <a name="save-device-inventory-filters"></a>Salva filtri inventario dispositivo

È possibile salvare un filtro o una combinazione di filtri necessari e riapplicarli nell'inventario dei dispositivi. Creare filtri più ampi in base a un determinato tipo di dispositivo o a filtri più stretti basati su un tipo specifico e un protocollo specifico.

I filtri salvati vengono salvati anche come gruppi di mappa del dispositivo. Questa funzionalità offre un livello aggiuntivo di granularità nella visualizzazione dei dispositivi di rete sulla mappa.

Per creare i filtri:

1. Nella colonna che si desidera filtrare selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. Nella finestra di dialogo **filtro** selezionare il tipo di filtro:

   - **Equals**: valore esatto in base al quale si desidera filtrare la colonna. Se ad esempio si filtra la colonna protocollo in base a **uguale** a e `value=ICMP` , nella colonna vengono visualizzati solo i dispositivi che utilizzano il protocollo ICMP.

   - **Contains**: valore contenuto tra gli altri valori nella colonna. Se ad esempio si filtra la colonna protocollo in base a **Contains** e `value=ICMP` , nella colonna vengono presentati i dispositivi che usano il protocollo ICMP come parte dell'elenco di protocolli utilizzati dal dispositivo.

3. Per organizzare le informazioni di colonna in base all'ordine alfabetico, selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Organizzare l'ordine selezionando le :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: frecce e.

4. Per salvare un nuovo filtro, definire il filtro e selezionare **Salva con nome**.

5. Per modificare le definizioni di filtro, modificare le definizioni e selezionare **Salva modifiche**.

Per visualizzare i filtri:

- Aprire il riquadro sinistro e visualizzare i filtri salvati:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Visualizzare i filtri dal riquadro a sinistra.":::

### <a name="view-filtered-information-as-a-map-group"></a>Visualizzare le informazioni filtrate come gruppo mappa

Quando si passa alla visualizzazione mappa, i dispositivi filtrati vengono evidenziati e filtrati. Il gruppo di filtri salvato viene visualizzato nel menu laterale sotto il gruppo **filtri inventario dispositivi** .

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Visualizzare i filtri quando si usa la vista mappa.":::

## <a name="learn-windows-registry-details"></a>Informazioni sui dettagli del registro di sistema di Windows

Oltre ad acquisire familiarità con i dispositivi, è possibile individuare le workstation e i server Microsoft Windows. Questi dispositivi vengono visualizzati anche nell'inventario dei dispositivi. Dopo aver apprendere i dispositivi, è possibile arricchire l'inventario dei dispositivi con informazioni dettagliate su Windows, ad esempio:

- Versione di Windows installata

- Applicazioni installate

- Informazioni a livello di patch

- Aprire le porte

- Informazioni più affidabili sulle versioni del sistema operativo

Per il recupero di queste informazioni sono disponibili due opzioni:

- Polling attivo tramite analisi WMI pianificate. 

- Sondaggio locale distribuendo ed eseguendo uno script nel dispositivo. L'utilizzo di script locali ignora i rischi correlati all'esecuzione del polling WMI su un endpoint. È utile anche per le reti regolamentate con le cascate e gli elementi unidirezionali.

Questo articolo descrive come rilevare localmente il registro degli endpoint di Windows con uno script. Queste informazioni verranno usate per la generazione di avvisi, notifiche, report data mining, valutazioni dei rischi e report vettoriali di attacco.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Schermata di data mining.":::

È possibile esaminare i sistemi operativi Windows seguenti:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Prima di iniziare

Per usare lo script, è necessario soddisfare i requisiti seguenti:

- Per eseguire lo script nel dispositivo sono necessarie le autorizzazioni di amministratore.

- Il sensore dovrebbe avere già appreso il dispositivo Windows. Ciò significa che se il dispositivo esiste già, lo script recupererà le informazioni.

- Un sensore sta monitorando la rete a cui è connesso il PC Windows.

### <a name="acquire-the-script"></a>Acquisire lo script

Per ricevere lo script, [contattare il supporto](mailto:support.microsoft.com)tecnico.

### <a name="deploy-the-script"></a>Distribuire lo script

È possibile distribuire lo script una sola volta o pianificare le query in corso usando metodi e strumenti di distribuzione automatici standard.

### <a name="about-the-script"></a>Informazioni sullo script

- Lo script viene eseguito come utilità e non come programma installato. L'esecuzione dello script non influisce sull'endpoint.

- I file generati dallo script rimangono nell'unità locale fino a quando non vengono eliminati.

- I file generati dallo script sono posizionati l'uno accanto all'altro. Non separarli.

- Se si esegue di nuovo lo script nello stesso percorso, questi file vengono sovrascritti.

Per eseguire lo script:  

1. Copiare lo script in un'unità locale e decomprimerlo. Vengono visualizzati i file seguenti:

    - start.bat

    - settings.js

    - Data. bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Visualizzazione dei file in Esplora file.":::

2. Eseguire il `run.bat` file.

3. Dopo il probe del registro di sistema, il file di snapshot CX viene visualizzato con le informazioni del registro di sistema.

4. Il nome del file indica il nome del sistema e la data e l'ora dello snapshot. Un nome file di esempio è `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Importa i dettagli del dispositivo

Le informazioni apprese su ogni endpoint devono essere importate nel sensore.

I file generati dalle query possono essere inseriti in una cartella a cui è possibile accedere dai sensori. Usare i metodi e gli strumenti standard e automatici per spostare i file da ogni endpoint di Windows nel percorso in cui verranno importati nel sensore.

Non aggiornare i nomi di file.

Per importare:

1. Selezionare **Importa impostazioni** dalla finestra di dialogo **Importa configurazione di Windows** .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importare le configurazioni di Windows.":::

2. Selezionare **Aggiungi** e quindi selezionare tutti i file (CTRL + A).

3. Selezionare **Chiudi**. Vengono importate le informazioni del registro di sistema. Se si verifica un problema durante il caricamento di uno dei file, viene informato quale caricamento del file non è riuscito.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="Il caricamento dei file aggiunti è riuscito.":::

## <a name="export-device-inventory-information"></a>Esporta informazioni inventario dispositivo

È possibile esportare le informazioni di inventario dei dispositivi in un file di Excel.

Per esportare un file CSV:

- Nel menu in alto a destra dell'inventario del dispositivo selezionare :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . Il report CSV viene generato e scaricato.

## <a name="see-also"></a>Vedi anche

[Esaminare tutti i rilevamenti dei sensori aziendali un inventario di dispositivi](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Utilizzare le viste della mappa del sito](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
