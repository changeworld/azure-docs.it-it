---
title: Usare la mappa dei dispositivi rilevati dai sensori
description: La mappa del dispositivo fornisce una rappresentazione grafica dei dispositivi di rete rilevati. Usare la mappa per analizzare e gestire le informazioni sul dispositivo, le sezioni di rete e generare report.
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: f7579cbca618baef404236556993c9831dd84bdf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784595"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Esaminare i rilevamenti dei sensori nella mappa del dispositivo

La mappa del dispositivo fornisce una rappresentazione grafica dei dispositivi di rete rilevati. Usare la mappa per:

  - Recuperare, analizzare e gestire le informazioni sul dispositivo.

  - Analizzare le sezioni di rete, ad esempio gruppi specifici di interesse o livelli Purdue.

  - Generare report, ad esempio esportare i dettagli e i riepiloghi del dispositivo.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Screenshot della mappa del dispositivo.":::

Per accedere alla mappa:

  - Selezionare **mappa del dispositivo** dalla schermata principale della console.

## <a name="map-search-and-layout-tools"></a>Mappare gli strumenti di layout e di ricerca

Gli strumenti seguenti vengono usati per lavorare nella mappa.

Il ruolo utente determina quali strumenti sono disponibili nella finestra Mappa del dispositivo. Per informazioni dettagliate sui ruoli utente, vedere [creare e gestire gli utenti](how-to-create-and-manage-users.md) .

| Simbolo | Descrizione |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Cerca per indirizzo IP o indirizzo MAC per un dispositivo specifico. Immettere l'indirizzo IP o l'indirizzo MAC nella casella di testo. La mappa Visualizza il dispositivo cercato con i dispositivi connessi. |
| Evidenziare e filtrare i gruppi <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Screenshot del gruppo in evidenza e filtri."::: | Filtrare o evidenziare la mappa in base ai gruppi di dispositivi predefiniti e personalizzati. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Comprimi visualizzazione, per abilitare una visualizzazione mirata nei dispositivi OT e raggruppare i dispositivi IT.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Mantenere la disposizione corrente del dispositivo nella mappa. Se, ad esempio, si trascinano i dispositivi in nuove posizioni sulla mappa, i dispositivi rimarranno in questi percorsi alla chiusura della mappa. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Adatta allo schermo |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -Visualizzare il livello Purdue identificato per questo dispositivo, tra cui automatico, controllo dei processi, supervisione ed Enterprise <br /> -Visualizzare le connessioni tra i dispositivi.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Mostra o Nascondi tra broadcast e multicast. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filtrare i dispositivi sulla mappa in base all'ora dell'ultima comunicazione con altri dispositivi. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="notifiche" border="false"::: | Visualizzare le notifiche relative a un dispositivo. Ad esempio, se è stato rilevato un nuovo IP per un dispositivo con un indirizzo MAC esistente |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Export" border="false"::: | Esportare/importare le informazioni sul dispositivo. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | Visualizzare le proprietà del dispositivo di base per i dispositivi selezionati. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Zoom" border="false"::: avanti o :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="Zoom" border="false"::: indietro | Esegue lo zoom avanti o indietro dei dispositivi nella mappa. |

## <a name="view-ot-elements-only"></a>Visualizza solo elementi OT

Per impostazione predefinita, i dispositivi IT vengono aggregati automaticamente per subnet, in modo che la visualizzazione mappa sia incentrata sulle reti OT e ICS. La presentazione degli elementi della rete IT è compressa a un valore minimo, che riduce il numero totale di dispositivi presentati sulla mappa e fornisce un quadro chiaro degli elementi di rete OT e ICS.

Ogni subnet viene presentata come una singola entità nella mappa del dispositivo, inclusa una funzionalità di compressione ed espansione interattiva per esaminare i dettagli di una subnet IT e viceversa.

Nella figura seguente è illustrata una subnet IT compressa con 27 elementi di rete IT.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="subnet IT compressa con 27 elementi rete IT":::

Per abilitare la funzionalità di compressione delle reti IT:

- Nella finestra **Impostazioni sistema** verificare che la funzionalità di raggruppamento attiva/Nascondi reti IT sia abilitata.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Finestra Impostazioni sistema":::

Per espandere una subnet IT:

1. Per distinguere tra le reti IT e OT, dalla schermata Impostazioni sistema selezionare **subnet**.

   > [!NOTE]
   > Si consiglia di denominare ogni subnet con nomi significativi all'utente in modo da identificare facilmente per distinguere le reti IT e OT.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Configurazione delle subnet":::

2. Nella finestra di **configurazione modifica subnet** deselezionare la casella di controllo **subnet ICS** per ogni subnet che si desidera definire come subnet it. Le subnet IT appaiono compresse nella mappa dei dispositivi con le notifiche per i dispositivi ICS, ad esempio un controller o un PLC, nelle reti IT.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Modificare la configurazione delle subnet":::

3. Per espandere la rete IT sulla mappa, nella finestra dispositivi fare clic con il pulsante destro del mouse su di essa e scegliere **Espandi rete**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Espandi la tua visualizzazione della tua rete.":::

4. Viene visualizzata una finestra di conferma che informa che non è possibile ripetere la modifica del layout.

5. Selezionare **OK**. Gli elementi della subnet IT vengono visualizzati sulla mappa.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Per comprimere una subnet IT:

1.  Nel riquadro sinistro selezionare **Devices (dispositivi**).

2. Nella finestra dispositivi selezionare l'icona Comprimi. Il numero in rosso indica il numero di subnet IT espanse attualmente visualizzate sulla mappa.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Finestra Dispositivo":::

3. Selezionare le subnet che si desidera comprimere o selezionare **Comprimi tutto**. La subnet selezionata viene visualizzata compressa sulla mappa.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Comprimi tutto":::

L'icona Comprimi viene aggiornata con il numero aggiornato di subnet IT espanse.

## <a name="view-or-highlight-device-groups"></a>Visualizzare o evidenziare i gruppi di dispositivi

È possibile personalizzare la visualizzazione della mappa in base ai gruppi di dispositivi. Ad esempio, gruppi di dispositivi associati a un protocollo OT, una VLAN o una subnet specifica. Sono disponibili gruppi predefiniti e è possibile creare gruppi personalizzati.

Visualizza gruppi per:

  - **Evidenziazione:** Evidenziare i dispositivi che appartengono a un gruppo specifico in blu.

  - **Filtro:** Visualizza solo i dispositivi che appartengono a un gruppo specifico.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Visualizzazione standard della porta":::

Sono disponibili i gruppi predefiniti seguenti:

| Nome gruppo | Descrizione |
|--|--|
| **Applicazioni note** | Dispositivi che utilizzano porte riservate, ad esempio TCP.  |
| **porte non standard (impostazione predefinita)** | Dispositivi che utilizzano porte o porte non standard a cui non è stato assegnato un alias. |
| **Protocolli OT (impostazione predefinita)** | Dispositivi che gestiscono il traffico OT noto. |
| **Authorization (impostazione predefinita)** | Dispositivi che sono stati individuati nella rete durante il processo di apprendimento o sono stati ufficialmente autorizzati in rete. |
| **Filtri inventario dispositivi** | Dispositivi raggruppati in base ai filtri salvati nella tabella inventario dispositivo. |
| **Intervalli di polling** | Dispositivi raggruppati in base a intervalli di polling. Gli intervalli di polling vengono generati automaticamente in base a canali ciclici o punti. Ad esempio, 15,0 secondi, 3,0 secondi, 1,5 secondi o qualsiasi intervallo. La revisione di queste informazioni consente di apprendere se i sistemi eseguono il polling troppo rapidamente o lentamente. |
| **Programmazione** | Stazioni di progettazione e computer di programmazione. |
| **Subnet** | Dispositivi che appartengono a una subnet specifica. |
| **VLAN** | Dispositivi associati a un ID VLAN specifico. |
| **Connessioni tra subnet** | Dispositivi che comunicano da una subnet a un'altra subnet. |
| **Avvisi bloccati** | Dispositivi per i quali l'utente ha aggiunto un avviso. |
| **Simulazioni di vettori di attacco** | Dispositivi vulnerabili rilevati nei report vettoriali di attacco. Per visualizzare questi dispositivi sulla mappa, selezionare la casella di controllo **Visualizza nella mappa del dispositivo** durante la generazione del vettore di attacco. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Aggiungere simulazioni vettoriali di attacco":::. |
| **Ultima visualizzazione** | Dispositivi raggruppati in base all'intervallo di tempo in cui sono stati visualizzati, ad esempio: un'ora, sei ore, un giorno, sette giorni. |
| **Non in Active Directory** | Tutti i dispositivi non PLC che non comunicano con il Active Directory. |

Per evidenziare o filtrare i dispositivi:

1. Selezionare **mappa del dispositivo** dal menu laterale.

2. Selezionare l'icona del filtro. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menu":::

3. Dal riquadro gruppi selezionare il gruppo che si desidera evidenziare o filtrare i dispositivi.

4. Selezionare **evidenzia** o **Filtra**. Consente di impostare la stessa selezione per rimuovere l'evidenziazione o il filtro.

## <a name="define-custom-groups"></a>Definire gruppi personalizzati

Oltre a visualizzare i gruppi predefiniti, è possibile definire gruppi personalizzati. I gruppi vengono visualizzati nei report mapping dispositivi, inventario dispositivi e data mining.

> [!NOTE]
> È anche possibile creare gruppi dall'inventario dei dispositivi.

Per creare un gruppo:

1. Selezionare **dispositivi** dal menu laterale. Viene visualizzata la mappa del dispositivo.

1. Selezionare :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="impostazione gruppo"::: per visualizzare le impostazioni dei gruppi.

1. Selezionare :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="gruppi"::: per creare un nuovo gruppo personalizzato.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Creare una schermata di gruppo personalizzata":::

1. Aggiungere il nome del gruppo, usare un massimo di 30 caratteri.

1. Selezionare i dispositivi pertinenti, come indicato di seguito:

   - Aggiungere i dispositivi da questo menu selezionandola dall'elenco (selezionare il pulsante freccia),<br /> Oppure <br /> 
   - Aggiungere i dispositivi da questo menu copiando tali dispositivi da un gruppo selezionato (selezionare il pulsante freccia)

1. Selezionare **Aggiungi gruppo** per aggiungere gruppi esistenti a gruppi personalizzati.

### <a name="add-devices-to-a-custom-group"></a>Aggiungere dispositivi a un gruppo personalizzato

È possibile aggiungere dispositivi a un gruppo personalizzato o creare un nuovo gruppo personalizzato e il dispositivo.

1. Fare clic con il pulsante destro del mouse su uno o più dispositivi sulla mappa.

1. Selezionare **Aggiungi al gruppo**.

1. Immettere un nome di gruppo nel campo gruppo e selezionare +. Viene visualizzato il nuovo gruppo. Se il gruppo esiste già, verrà aggiunto al gruppo personalizzato esistente.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Nome gruppo":::

1. Aggiungere i dispositivi a un gruppo ripetendo i passaggi 1-3.

## <a name="map-zoom-views"></a>Viste Zoom mappa

L'uso delle visualizzazioni mappa consente di velocizzare le analisi scientifiche durante l'analisi di reti di grandi dimensioni.

È possibile visualizzare tre visualizzazioni dei dettagli del dispositivo:

  - [Visualizzazione di Bird-Eye](#birds-eye-view)

  - [Tipo di dispositivo e visualizzazione connessione](#device-type-and-connection-view)

  - [Visualizzazione dettagliata](#detailed-view)

### <a name="birds-eye-view"></a>Visualizzazione di Bird-Eye

Questa visualizzazione offre una visualizzazione immediata dei dispositivi rappresentati come segue:

  - I punti rossi indicano i dispositivi con avvisi

  - I punti con stella indicano i dispositivi contrassegnati come importanti

  - I punti neri indicano i dispositivi senza avvisi

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Visualizzazione Bird Eye":::

### <a name="device-type-and-connection-view"></a>Tipo di dispositivo e visualizzazione connessione 

Questa visualizzazione presenta i dispositivi rappresentati come icone sulla mappa per evidenziare i dispositivi con avvisi, tipi di dispositivi e dispositivi connessi.

  - I dispositivi con avvisi vengono visualizzati con un anello rosso

  - I dispositivi senza avvisi vengono visualizzati con un anello grigio

  - I dispositivi visualizzati come stella erano contrassegnati come importanti

L'icona del tipo di dispositivo viene visualizzata con i dispositivi connessi.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="visualizzazione connessione":::

### <a name="detailed-view"></a>Visualizzazione dettagliata 

La visualizzazione dettagliata presenta i dispositivi e le etichette e gli indicatori del dispositivo con le seguenti informazioni:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Visualizzazione dettagliata":::

### <a name="control-the-zoom-view"></a>Controllare la visualizzazione zoom

La vista mappa visualizzata dipende dal livello di zoom della mappa. Il passaggio tra le visualizzazioni della mappa viene eseguito cambiando i livelli di zoom.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Controllare la visualizzazione zoom":::

### <a name="enable-simplified-zoom-views"></a>Abilita visualizzazioni zoom semplificate

Gli amministratori che desiderano gli analisti della sicurezza e gli utenti RO possono accedere alle viste di connessione di tipo Bird-Eye e Device e Type, per abilitare l'opzione di visualizzazione semplificata.

Per abilitare le visualizzazioni mappa semplificate:

  - Selezionare **impostazioni di sistema** , quindi impostare l'opzione di **visualizzazione mappa semplificata** .

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Semplificare la visualizzazione mappa":::

## <a name="learn-more-about-devices"></a>Altre informazioni sui dispositivi

Sono disponibili una vasta gamma di strumenti per ottenere altre informazioni sui dispositivi:

- [Etichette e indicatori del dispositivo](#device-labels-and-indicators)

- [Visualizzazioni rapide del dispositivo](#device-quick-views)

- [Visualizzare e gestire le proprietà dei dispositivi](#view-and-manage-device-properties)

- [Visualizza tipi di dispositivi](#view-device-types)

- [Backplane](#backplane-properties)

- [Visualizzare una sequenza temporale degli eventi per il dispositivo](#view-a-timeline-of-events-for-the-device)

- [Analizzare i dettagli e le modifiche della programmazione](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Etichette e indicatori del dispositivo

I dispositivi sulla mappa possono visualizzare le etichette e gli indicatori seguenti:

| Etichetta del dispositivo | Descrizione |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="Nome host IP"::: | Nome host e indirizzo IP dell'indirizzo IP o indirizzi subnet |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Numero di avvisi"::: | Numero di avvisi associati al dispositivo |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Icona del tipo di dispositivo, ad esempio archiviazione, PLC o storico. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="dispositivi raggruppati"::: | Numero di dispositivi raggruppati in una subnet in una rete IT. Nell'esempio 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="periodo di apprendimento del dispositivo"::: | Un dispositivo rilevato dopo il periodo di apprendimento e non è stato autorizzato come dispositivo di rete. |
| Linea continua | Connessione logica tra dispositivi |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Nuovo dispositivo"::: | Nuovo dispositivo individuato dopo il completamento dell'apprendimento. |

### <a name="device-quick-views"></a>Visualizzazioni rapide del dispositivo

Accedere alle proprietà e alle connessioni del dispositivo dalla mappa.

Per aprire il menu Proprietà rapide:

  - Selezionare il menu Proprietà rapide menu :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="Proprietà rapide":::.

#### <a name="quick-device-properties"></a>Proprietà dispositivo rapido

Selezionare un dispositivo o più dispositivi mentre è aperta la schermata Proprietà rapide per visualizzare le informazioni di rilievo di questi dispositivi:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Proprietà dispositivo rapido":::

#### <a name="quick-connection-properties"></a>Proprietà connessione rapida

Selezionare una connessione mentre la schermata delle proprietà rapide è aperta per visualizzare i protocolli usati in questa connessione e l'ultima volta che sono stati visualizzati:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Proprietà connessione rapida":::

## <a name="view-and-manage-device-properties"></a>Visualizzare e gestire le proprietà dei dispositivi

È possibile visualizzare i dispositivi per ogni dispositivo visualizzato sulla mappa. Ad esempio, il nome, il tipo o il sistema operativo del dispositivo o il firmware o fornitore.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Visualizzare e gestire le proprietà dei dispositivi":::

Le informazioni seguenti possono essere aggiornate manualmente. Le informazioni immesse manualmente sostituiranno le informazioni individuate da Defender per l'it.

  - Nome

  - Tipo

  - OS

  - Livello Purdue

  - Descrizione

| Elemento | Descrizione |
|--|--|
| Basic Information | Informazioni di base necessarie. |
| Nome | Nome del dispositivo. <br /> Per impostazione predefinita, il sensore individua il nome del dispositivo come definito nella rete. Ad esempio, un nome definito nel server DNS. <br /> Se non è stato definito alcun nome di questo tipo, l'indirizzo IP del dispositivo viene visualizzato in questo campo. <br /> È possibile modificare manualmente il nome di un dispositivo. Assegnare ai dispositivi nomi significativi che riflettono le proprie funzionalità. |
| Tipo | Il tipo di dispositivo rilevato dal sensore. <br /> Per altre informazioni, vedere [visualizzare i tipi di dispositivi](#view-device-types). |
| Vendor | Fornitore del dispositivo. Questa operazione è determinata dai caratteri iniziali dell'indirizzo MAC del dispositivo. Questo campo è di sola lettura. |
| Sistema operativo | Sistema operativo del dispositivo rilevato dal sensore. |
| Livello Purdue | Il livello Purdue identificato dal sensore per questo dispositivo, tra cui: <br /> -Automatico <br /> -Controllo processo <br /> -Supervisione <br /> - Enterprise |
| Descrizione | Un campo di testo libero. <br /> Aggiungere altre informazioni sul dispositivo. |
| Attributi | Tutte le informazioni aggiuntive individuate sul dispositivo durante il periodo di apprendimento e non appartenenti ad altre categorie vengono visualizzate nella sezione attributi. <br /> Le informazioni sono RO. |
| Impostazioni | È possibile modificare manualmente le impostazioni del dispositivo per evitare falsi positivi: <br /> - **Dispositivo autorizzato**: durante il periodo di apprendimento tutti i dispositivi individuati nella rete vengono identificati come dispositivi autorizzati. Quando un dispositivo viene individuato dopo il periodo di apprendimento, viene visualizzato come dispositivo non autorizzato per impostazione predefinita. Questa definizione può essere modificata manualmente. <br /> - **Noto come scanner**: abilitare questa opzione se si è certi che questo dispositivo è noto come scanner e non è necessario segnalarlo. <br /> - **Device Programming**: abilitare questa opzione se si è certi che questo dispositivo è noto come dispositivo di programmazione e viene usato per apportare modifiche alla programmazione. L'identificazione come un dispositivo di programmazione impedirà agli avvisi di programmare modifiche provenienti da questo asset. |
| Gruppi personalizzati | Gruppi personalizzati nella mappa del dispositivo a cui partecipa questo dispositivo. |
| State | Sicurezza e stato di autorizzazione del dispositivo: <br /> -Lo stato è `Secured` quando non sono presenti avvisi <br /> -Quando sono presenti avvisi relativi al dispositivo, viene visualizzato il numero di avvisi <br /> -Lo stato `Unauthorized` viene visualizzato per i dispositivi aggiunti alla rete dopo il periodo di apprendimento. È possibile definire manualmente il dispositivo come `Authorized Device` nelle impostazioni <br /> -Se l'indirizzo del dispositivo è definito come indirizzo dinamico, `DHCP` viene aggiunto allo stato. |


| Rete | Descrizione |
|--|--|
| Interfacce | Interfacce del dispositivo. Campo RO. |
| Protocolli | Protocolli usati dal dispositivo. Campo RO. |
| Firmware | Se sono disponibili informazioni sul backplane, le informazioni sul firmware non verranno visualizzate. |
| Indirizzo | Indirizzo IP del dispositivo. |
| Serial | Numero di serie del dispositivo. |
| Indirizzo modulo | Il modello e il numero di slot o l'ID del dispositivo. |
| Modello | Numero del modello del dispositivo. |
| Versione del firmware | Numero di versione del firmware. |

Per visualizzare le informazioni sul dispositivo:

1. Selezionare **dispositivi** dal menu laterale.

2. Fare clic con il pulsante destro del mouse su un dispositivo e scegliere **Visualizza proprietà**. Viene visualizzata la Finestra Proprietà del dispositivo.

3. Selezionare l'avviso richiesto nella parte inferiore della finestra per visualizzare informazioni dettagliate sugli avvisi relativi a questo dispositivo.

### <a name="view-device-types"></a>Visualizza tipi di dispositivi

Il tipo di dispositivo viene identificato automaticamente dal sensore durante il processo di individuazione del dispositivo. È possibile modificare il tipo manualmente.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Visualizza tipi di dispositivi":::

Nella tabella seguente vengono presentati tutti i tipi del sistema:

| Category | Tipo di dispositivo |
|--|--|
| ICS | Stazione di progettazione <br /> PLC <br />Historian <br />HMI <br />IED <br />Controller DCS <br />RTU <br />Sistema di assemblaggio industriale <br />Scalabilità industriale <br />Robot industriale <br />Slot <br />Contatore <br />Unità frequenza variabile  <br />Controller robot <br />Unità servo <br />Dispositivo pneumatico <br />Testo scorrevole |
| IT | Controller di dominio <br />Server di database <br />Workstation <br />Server <br />Stazione terminale <br />Archiviazione <br />Smart Phone <br />Tablet <br />Server di backup |
| IoT | Videocamera IP <br />Stampante  <br />Clock Punch <br />ATM <br />Smart TV <br />Console del gioco <br />DVR <br />Pannello di controllo della porta <br />HVAC <br />Thermostat <br />Allarme antincendio <br />Luce intelligente <br />Switch intelligente <br />Fire Detector <br />Telefono IP <br />Sistema di allarme <br />Sirena di allarme <br />Motion Detector <br />Elevator (Montacarichi) <br />Sensore di umidità <br />Scanner di codice a barre <br />Gruppo di continuità <br />Sistema di contatori persone <br />Intercom <br />Tornello |
| Rete | Punto di accesso wireless <br />Router <br />Opzione <br />Firewall <br />Gateway VPN <br />Server NTP <br />Ananas WiFi <br />Posizione fisica <br />Adapter I/O <br /> Convertitore di protocolli |

Per visualizzare le informazioni sul dispositivo:

1.  Selezionare **dispositivi** dal menu laterale.

2. Fare clic con il pulsante destro del mouse su un dispositivo e scegliere **Visualizza proprietà**. Viene visualizzata la Finestra Proprietà del dispositivo.

3. Selezionare l'avviso richiesto per visualizzare informazioni dettagliate sugli avvisi per il dispositivo.

### <a name="backplane-properties"></a>Proprietà backplane

Se un PLC contiene più moduli separati in rack e slot, le caratteristiche potrebbero variare tra le schede dei moduli. Se, ad esempio, l'indirizzo IP e l'indirizzo MAC sono gli stessi, il firmware potrebbe essere diverso.

È possibile usare l'opzione backplane per esaminare più controller/schede e i dispositivi annidati come un'entità con diverse definizioni. Ogni slot nella visualizzazione backplane rappresenta i dispositivi sottostanti, ovvero i dispositivi che sono stati individuati dietro di esso.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Proprietà backplane":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Proprietà del dispositivo backplane":::

Un backplane può contenere fino a 30 schede controller e fino a 30 unità rack. Il numero totale di dispositivi inclusi in più livelli può essere fino a 200 dispositivi.

Il riquadro backplane viene visualizzato nel dispositivo Finestra Proprietà quando vengono rilevati i dettagli del backplane.

Ogni slot viene visualizzato con il numero di dispositivi sottostanti e l'icona che mostra il tipo di modulo.

| Icona | Tipo di modulo |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Alimentatore"::: | Alimentatore |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="I/O analogico"::: | I/O analogico |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Adattatore di comunicazione"::: | Adattatore di comunicazione |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="I/O digitale"::: | I/O digitale |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Generico"::: | Generico |

Quando si seleziona uno slot, vengono visualizzati i dettagli dello slot:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="Selezionare uno slot":::

Per visualizzare i dispositivi sottostanti dietro lo slot, selezionare **Visualizza sulla mappa**. Lo slot viene visualizzato nella mappa del dispositivo con tutti i moduli e i dispositivi sottostanti connessi.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="VISUALIZZA SULLA MAPPA":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Visualizzare una sequenza temporale degli eventi per il dispositivo

Visualizzare una cronologia degli eventi associati a un dispositivo.

Per visualizzare la sequenza temporale:

1. Fare clic con il pulsante destro del mouse su un dispositivo dalla mappa.

2. Selezionare **Mostra eventi**. Viene visualizzata la finestra sequenza temporale eventi con informazioni sugli eventi rilevati per il dispositivo selezionato.

Per informazioni dettagliate, vedere la [sequenza temporale degli eventi](#event-timeline) .

## <a name="analyze-programming-details-and-changes"></a>Analizzare i dettagli e le modifiche della programmazione

Migliorare le analisi forensi visualizzando gli eventi di programmazione eseguiti sui dispositivi di rete e analizzando le modifiche del codice. Queste informazioni consentono di individuare attività di programmazione sospette, ad esempio:

  - Errore umano: un ingegnere sta programmando il dispositivo errato.

  - Automazione della programmazione danneggiata: la programmazione viene eseguita erroneamente a causa di un errore di automazione.

  - Sistemi hacker: utenti non autorizzati connessi a un dispositivo di programmazione.

È possibile visualizzare un dispositivo programmato e scorrere le varie modifiche di programmazione eseguite da altri dispositivi.

Consente di visualizzare il codice aggiunto, modificato, rimosso o ricaricato dal dispositivo di programmazione. Cercare le modifiche di programmazione in base a tipi di file, date o orari di interesse.

### <a name="when-to-review-programming-activity"></a>Quando esaminare l'attività di programmazione 

Potrebbe essere necessario esaminare l'attività di programmazione:

  - Dopo la visualizzazione di un avviso relativo alla programmazione non autorizzata

  - Dopo un aggiornamento pianificato ai controller

  - Quando un processo o un computer non funziona correttamente (per vedere chi ha eseguito l'ultimo aggiornamento e quando)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Programmazione del log delle modifiche":::

Altre opzioni consentono di:

  - Contrassegnare gli eventi di interesse con una stella.

  - Scaricare un file *. txt con il codice corrente.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Informazioni sugli eventi di programmazione autorizzati e non autorizzati 

Gli eventi di programmazione non autorizzati vengono eseguiti da dispositivi che non sono stati appresi o definiti manualmente come dispositivi di programmazione. Gli eventi di programmazione autorizzati vengono eseguiti da dispositivi che sono stati risolti o definiti manualmente come dispositivi di programmazione.

Nella finestra di analisi della programmazione vengono visualizzati gli eventi di programmazione autorizzati e non autorizzati.

### <a name="accessing-programming-details-and-changes"></a>Accesso ai dettagli e alle modifiche della programmazione

Accedere alla finestra analisi di programmazione dal:

- [Sequenza temporale eventi](#event-timeline)

- [Avvisi di programmazione non autorizzati](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Sequenza temporale eventi

Usare la sequenza temporale eventi per visualizzare una sequenza temporale degli eventi in cui sono state rilevate modifiche di programmazione.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Visualizzazione della sequenza temporale dell'evento.":::

### <a name="unauthorized-programming-alerts"></a>Avvisi di programmazione non autorizzati

Gli avvisi vengono attivati quando i dispositivi di programmazione non autorizzati eseguono attività di programmazione.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Avvisi di programmazione non autorizzati":::

> [!NOTE]
> È anche possibile visualizzare le informazioni di programmazione di base nel Finestra Proprietà del dispositivo e nell'inventario dei dispositivi.

### <a name="working-in-the-programming-timeline-window"></a>Uso della finestra sequenza temporale di programmazione

In questa sezione viene descritto come visualizzare i file di programmazione e confrontare le versioni. Consente di cercare file specifici inviati a un dispositivo programmato. Cerca i file in base a:

  - Data

  - Tipo file

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="finestra sequenza temporale di programmazione":::

|Tipo di sequenza temporale di programmazione | Descrizione |
|--|--|
| Dispositivo programmato | Fornisce informazioni dettagliate sul dispositivo che è stato programmato, inclusi il nome host e il file. |
| Eventi recenti | Visualizza gli eventi 50 più recenti rilevati dal sensore. <br />Per evidenziare un evento, passare il puntatore del mouse su di esso e fare clic sulla stella. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Gli ultimi 50 eventi possono essere visualizzati. |
| File | Visualizza i file rilevati per la data scelta e le dimensioni del file nel dispositivo programmato. <br /> Per impostazione predefinita, il numero massimo di file disponibili per la visualizzazione per ogni dispositivo è 300. <br /> Per impostazione predefinita, la dimensione massima del file per ogni file è 15 MB. |
| Stato file :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Le etichette dei file indicano lo stato del file nel dispositivo, tra cui: <br /> **Aggiunta**: il file è stato aggiunto all'endpoint nella data o nell'ora selezionata. <br /> **Aggiornamento**: il file è stato aggiornato nella data o nell'ora selezionata. <br /> **Deleted**: il file è stato rimosso. <br /> **Nessuna etichetta**: il file non è stato modificato.   |
| Dispositivo di programmazione | Il dispositivo che ha apportato la modifica alla programmazione. Più dispositivi potrebbero avere eseguito modifiche di programmazione in un dispositivo programmato. Vengono visualizzati il nome host, la data o l'ora di modifica e l'utente connesso. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Visualizza il file corrente installato nel dispositivo programmato. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Scaricare un file di testo del codice visualizzato. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Confronta il file corrente con il file rilevato in una data selezionata. |

### <a name="choose-a-file-to-review"></a>Scegliere un file da rivedere

Questa sezione descrive come scegliere un file da rivedere.

Per scegliere un file da rivedere:

1. Selezionare un evento dal riquadro **eventi recenti**

2. Selezionare un file nel riquadro file. Il file verrà visualizzato nel riquadro corrente.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Selezionare il file da utilizzare.":::

### <a name="compare-files"></a>Confrontare i file

In questa sezione viene descritto come confrontare i file di programmazione.

Per confrontare:

1. Selezionare un evento dal riquadro eventi recenti.

2. Selezionare un file dal riquadro file. Il file verrà visualizzato nel riquadro corrente. È possibile confrontare questo file con altri file.

3. Selezionare l'indicatore di confronto.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Indicatore di confronto":::

   Nella finestra vengono visualizzate tutte le date in cui è stato rilevato il file selezionato sul dispositivo programmato. È possibile che il file sia stato aggiornato sul dispositivo programmato da più dispositivi di programmazione.

   Il numero di differenze rilevate viene visualizzato nell'angolo superiore destro della finestra. Potrebbe essere necessario scorrere verso il basso per visualizzare le differenze.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Scorri verso il basso fino alla selezione":::

   Il numero viene calcolato dalle righe adiacenti del testo modificato. Se, ad esempio, sono state modificate otto righe consecutive di codice (eliminate, aggiornate o aggiunte), questo verrà calcolato come una differenza.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Visualizzazione della sequenza temporale di programmazione.":::

4. Selezionare una data. Il file rilevato nella data selezionata viene visualizzato nella finestra.

5. Il file selezionato dal riquadro eventi/file recenti viene sempre visualizzato a destra.

### <a name="device-programming-information-other-locations"></a>Informazioni sulla programmazione del dispositivo: altre località

Oltre a esaminare i dettagli nella sequenza temporale di programmazione, è possibile accedere alle informazioni di programmazione nel Finestra Proprietà del dispositivo e nell'inventario dei dispositivi.

| Tipo di dispositivo | Descrizione |
|--|--|
| Proprietà dei dispositivi | La finestra delle proprietà del dispositivo fornisce informazioni sull'ultimo evento di programmazione rilevato in device\. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Proprietà del dispositivo"::: |
| Inventario del dispositivo | L'inventario del dispositivo indica se il dispositivo è un device\. di programmazione :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Inventario dei dispositivi"::: |

## <a name="manage-device-information-from-the-map"></a>Gestire le informazioni sul dispositivo dalla mappa

Il sensore non aggiorna né influisca sui dispositivi direttamente nella rete. Le modifiche apportate qui incidono solo sul modo in cui analizza il dispositivo.

### <a name="delete-devices"></a>Eliminare dispositivi

Potrebbe essere necessario eliminare un dispositivo se le informazioni apprese non sono rilevanti. Ad esempio,

  - Un appaltatore partner presso una workstation di progettazione si connette temporaneamente per eseguire gli aggiornamenti della configurazione. Al termine dell'attività, il dispositivo viene rimosso.

  - A causa delle modifiche apportate alla rete, alcuni dispositivi non sono più connessi.

Se non si elimina il dispositivo, il sensore continuerà a monitorarlo. Dopo 60 giorni, viene visualizzata una notifica che consiglia di eliminare.

È possibile che venga visualizzato un avviso che indica che il dispositivo non risponde se un altro dispositivo tenta di accedervi. In questo caso, è possibile che la rete sia configurata in modo non conforme.

Il dispositivo verrà rimosso dai report mapping dispositivi, inventario dispositivi e data mining. Altre informazioni, ad esempio, le informazioni archiviate nei widget verranno mantenute.

Il dispositivo deve essere inattivo per almeno 10 minuti per eliminarlo.

Per eliminare un dispositivo dalla mappa del dispositivo:

1. Selezionare **dispositivi** dal menu laterale.

2. Fare clic con il pulsante destro del mouse su un dispositivo e scegliere **Elimina**.

### <a name="merge-devices"></a>Unisci dispositivi

In alcuni casi, potrebbe essere necessario unire i dispositivi. Questa operazione può essere necessaria se il sensore ha individuato entità di rete separate associate a un dispositivo univoco. Ad esempio,

  - Un PLC con quattro schede di rete.

  - Un portatile con Wi-Fi e scheda fisica.
  
  - Una workstation con due o più schede di rete.

Quando si esegue il merge, si indica al sensore di combinare le proprietà del dispositivo di due dispositivi in una sola. Quando si esegue questa operazione, i report del dispositivo Finestra Proprietà e del sensore verranno aggiornati con i dettagli della nuova proprietà del dispositivo.

Se, ad esempio, si uniscono due dispositivi, ognuno con un indirizzo IP, entrambi gli indirizzi IP verranno visualizzati come interfacce separate nel Finestra Proprietà del dispositivo. È possibile unire solo i dispositivi autorizzati.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Finestra Proprietà del dispositivo":::

La sequenza temporale dell'evento presenta l'evento di Unione.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Sequenza temporale dell'evento con eventi Uniti.":::

Non è possibile annullare l'Unione di un dispositivo. Se sono stati erroneamente Uniti due dispositivi, eliminare il dispositivo e attendere che il sensore rilevi entrambi.

Per unire i dispositivi:

1. Selezionare due dispositivi (fare clic con il tasto MAIUSC), quindi fare clic con il pulsante destro del mouse su uno di essi.

2. Selezionare **merge** per unire i dispositivi. L'operazione di merge può richiedere fino a 2 minuti.

3. Nella finestra di dialogo Imposta attributi del dispositivo Unione scegliere un nome di dispositivo.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="finestra di dialogo attributi":::

4. Selezionare **Salva**.

### <a name="authorize-and-unauthorize-devices"></a>Autorizzare e annullare l'autorizzazione dei dispositivi

Durante il periodo di apprendimento tutti i dispositivi individuati nella rete vengono identificati come dispositivi autorizzati. L'etichetta **autorizzata** non viene visualizzata in questi dispositivi nella mappa del dispositivo.

Quando un dispositivo viene individuato dopo il periodo di apprendimento, viene visualizzato come dispositivo non autorizzato. Oltre a visualizzare i dispositivi non autorizzati nella mappa, è anche possibile visualizzarli nell'inventario dei dispositivi.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Inventario dei dispositivi":::

**Nuovo dispositivo e non autorizzato**

I nuovi dispositivi rilevati dopo il periodo di apprendimento verranno visualizzati con un' `New` `Unauthorized` etichetta e.

Se si sposta un dispositivo sulla mappa o si modificano manualmente le proprietà del dispositivo, l' `New` etichetta viene rimossa dall'icona del dispositivo.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivi non autorizzati-vettori di attacco e report di valutazione dei rischi

I dispositivi non autorizzati sono inclusi nei report di valutazione dei rischi e nei report sui vettori di attacco.

- **Report vettoriali di attacco:** I dispositivi contrassegnati come non autorizzati vengono risolti nel vettore di attacco come sospetti dispositivi non autorizzati che potrebbero costituire una minaccia per la rete.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Visualizzazione i report vettoriali degli attacchi":::

- **Report di valutazione dei rischi:** I dispositivi contrassegnati come non autorizzati sono:

  - Identificato nei report di valutazione dei rischi

Per autorizzare o annullare l'autorizzazione manuale dei dispositivi:

1. Fare clic con il pulsante destro del mouse sul dispositivo sulla mappa e scegliere Annulla **autorizzazione**

### <a name="mark-devices-as-important"></a>Contrassegna i dispositivi come importanti

È possibile contrassegnare come importanti i dispositivi di rete significativi, ad esempio i server aziendali critici. Questi dispositivi sono contrassegnati con una stella sulla mappa. La stella varia in base al livello di zoom della mappa.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivi importanti-vettori di attacco e report di valutazione dei rischi

Quando si generano report di valutazione dei rischi e i vettori di attacco, vengono calcolati i dispositivi importanti.

  - I dispositivi dei report vettoriali di attacco contrassegnati come importanti vengono risolti nel vettore di attacco come destinazioni di attacco. 

  - Report di valutazione dei rischi: i dispositivi contrassegnati come importanti vengono calcolati quando si specifica il Punteggio di sicurezza nel report di valutazione dei rischi.

## <a name="generate-activity-reports-from-the-map"></a>Generare report attività dalla mappa

Genera un report attività per un dispositivo selezionato in base alle 1, 6, 12 o 24 ore. Sono disponibili le informazioni seguenti:

  - Categoria: informazioni di base sul rilevamento basate su scenari di traffico.

  - Dispositivi di origine e di destinazione

  - Dati: informazioni aggiuntive escluse.

  - Data e ora dell'ultima visualizzazione.

È possibile salvare il report come file di Microsoft Excel o Word.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Attività recente":::

Per generare un report attività per un dispositivo:

1. Fare clic con il pulsante destro del mouse su un dispositivo dalla mappa.

2. Selezionare un report attività.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Visualizzare un report dell'attività.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Generare report vettori di attacco dalla mappa

Simulare un report vettoriale di attacco per sapere se un dispositivo nella mappa selezionata è una destinazione di attacco vulnerabile.

I report vettoriali di attacco forniscono una rappresentazione grafica di una catena di vulnerabilità di dispositivi sfruttabili. Queste vulnerabilità possono consentire a un utente malintenzionato di accedere ai dispositivi di rete principali. Il simulatore di vettori di attacco calcola i vettori di attacco in tempo reale e analizza tutti i vettori di attacco per una destinazione specifica.

Per visualizzare un dispositivo in un report vettoriale di attacco:

1. Fare clic con il pulsante destro del mouse su un dispositivo dalla mappa.

2. Selezionare **simula i vettori di attacco**. Viene visualizzata la finestra di dialogo vettore di attacco con il dispositivo selezionato come destinazione di attacco.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Aggiungi simulazione vettoriale di attacco":::

3. Aggiungere i parametri rimanenti alla finestra di dialogo e selezionare **Aggiungi simulazione**.

## <a name="export-device-information-from-the-map"></a>Esporta le informazioni sul dispositivo dalla mappa

Esportare le informazioni sul dispositivo seguenti dalla mappa.

  - Dettagli dispositivo (Microsoft Excel)

  - Riepilogo dispositivo (Microsoft Excel)

  - Un file di Word con gruppi (Microsoft Word)

Per esportare:

1. Selezionare l'icona Esporta dalla mappa.

1. Selezionare un'opzione di esportazione.

## <a name="see-also"></a>Vedi anche

[Esaminare i rilevamenti dei sensori nell'inventario dei dispositivi](how-to-investigate-sensor-detections-in-a-device-inventory.md)
