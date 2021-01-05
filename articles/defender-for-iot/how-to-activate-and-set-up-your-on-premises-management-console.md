---
title: Attivare e configurare la console di gestione locale
description: L'attivazione e l'installazione della console di gestione garantiscono che i sensori siano registrati in Azure e inviino informazioni alla console di gestione locale e che la console di gestione locale esegua le attività di gestione sui sensori connessi.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/24/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 56092d61e1eed4e4ebe4f8cb7f97d01e91e8543e
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841333"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Attivare e configurare la console di gestione locale 

L'attivazione e l'installazione della console di gestione locale assicurano che:

- I dispositivi di rete monitorati tramite sensori connessi sono registrati con un account Azure.

- I sensori inviano informazioni alla console di gestione locale.

- La console di gestione locale esegue attività di gestione sui sensori connessi.

- È stato installato un certificato SSL.

## <a name="sign-in-for-the-first-time"></a>Accedere per la prima volta

Per accedere alla console di gestione:

- Aprire un Web browser e immettere l'indirizzo IP e la password ricevuti per la console di gestione locale durante l'installazione del sistema. Se si dimentica la password, selezionare **Ripristina password** e vedere [ripristino della password](how-to-manage-the-on-premises-management-console.md#password-recovery).

## <a name="upload-an-activation-file"></a>Carica un file di attivazione

Dopo aver eseguito l'accesso per la prima volta, attivare la console di gestione locale scaricando un file di attivazione dalla pagina dei **prezzi** di Azure Defender per il portale. Questo file contiene i dispositivi aggregati di cui è stato eseguito il commit definiti durante il processo di onboarding. **Dispositivi** di cui è stato eseguito il commit indica il numero di dispositivi che Defender per l'Internet delle cose monitorerà per sottoscrizione

Per caricare un file di attivazione:

1. Vai alla pagina dei **prezzi** di Defender for all.
1. Selezionare la scheda **scaricare il file di attivazione per la console di gestione** . Il file di attivazione viene scaricato.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Scaricare il file di attivazione.":::

1. Selezionare **impostazioni di sistema** dalla console di gestione.
1. Selezionare **Activation**.
1. Selezionare **Scegli un file** e selezionare il file salvato.

Dopo l'attivazione iniziale, il numero di dispositivi monitorati può superare il numero di dispositivi di cui è stato eseguito il commit definiti durante l'onboarding. Questo potrebbe accadere, ad esempio, se si connettono più sensori alla console di gestione. In caso di discrepanza tra il numero di dispositivi monitorati e il numero di dispositivi di cui è stato eseguito il commit, viene visualizzato un avviso nella console di gestione. In questo caso, è necessario caricare un nuovo file di attivazione.

## <a name="set-up-a-certificate"></a>Configurare un certificato

Dopo l'installazione della console di gestione, viene generato e utilizzato un certificato autofirmato locale per accedere alla console. Quando un amministratore accede alla console di gestione per la prima volta, all'utente viene richiesto di caricare un certificato SSL/TLS. Si consiglia di utilizzare un certificato firmato da un'autorità di certificazione attendibile senza utilizzare il certificato autofirmato generato localmente.

Sono disponibili due livelli di sicurezza:

- Soddisfare i requisiti specifici di certificato e crittografia richiesti dall'organizzazione caricando il certificato firmato da un'autorità di certificazione.
- Consente la convalida tra la console di gestione e i sensori connessi. La convalida viene valutata in base a un elenco di revoche di certificati e alla data di scadenza del certificato. *Se la convalida ha esito negativo, la comunicazione tra la console di gestione e il sensore viene interrotta e viene visualizzato un errore di convalida nella console.* Questa opzione è abilitata per impostazione predefinita dopo l'installazione.  

La console supporta i tipi di certificati seguenti:

- Infrastruttura a chiave privata ed Enterprise (PKI privata)
- Infrastruttura a chiave pubblica (PKI pubblico)
- Generato localmente nel dispositivo (autofirmato locale) 

  > [!IMPORTANT]
  > Si consiglia di non usare un certificato autofirmato. Il certificato non è sicuro e deve essere usato solo per gli ambienti di test. Il proprietario del certificato non può essere convalidato e non è possibile mantenere la sicurezza del sistema. Non usare mai questa opzione per le reti di produzione.

Per caricare un certificato:

1. Quando viene richiesto dopo l'accesso, definire un nome di certificato.
1. Caricare i file CRT e Key.
1. Immettere una passphrase e caricare un file PEM, se necessario.

Potrebbe essere necessario aggiornare la schermata dopo aver caricato il certificato firmato da un'autorità di certificazione.

Per disabilitare la convalida tra la console di gestione e i sensori connessi:

1. Selezionare **Avanti**.
1. Disattivare l'interruttore **Abilita convalida a livello di sistema** .

Per informazioni sul caricamento di un nuovo certificato, dei file di certificato supportati e degli elementi correlati, vedere [Manage the on-premises Management Console](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Connettere i sensori alla console di gestione locale

È necessario assicurarsi che i sensori inviino informazioni alla console di gestione locale e che la console di gestione locale sia in grado di eseguire backup, gestire gli avvisi ed eseguire altre attività sui sensori. A tale scopo, utilizzare le procedure riportate di seguito per verificare di aver creato una connessione iniziale tra i sensori e la console di gestione locale.

Sono disponibili due opzioni per la connessione di Azure Defender per i sensori Internet alla console di gestione locale:

- Connettersi dalla console dei sensori

- Connettersi tramite tunneling

Dopo la connessione, è necessario configurare un sito con questi sensori.

### <a name="connect-sensors-from-the-sensor-console"></a>Connettere i sensori dalla console dei sensori

Per connettere sensori specifici alla console di gestione locale dalla console del sensore:

1. Nel riquadro sinistro della console del sensore selezionare impostazioni di **sistema**.

2. Selezionare **connessione alla gestione**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Screenshot della finestra di stato di una console di gestione locale, mostrando non connesso.":::

3. Nella casella di testo **Indirizzo** immettere l'indirizzo IP della console di gestione locale a cui si desidera connettersi.

4. Selezionare **Connetti**. Lo stato cambia:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Screenshot della finestra di stato di una console di gestione locale, che mostra connessa.":::

### <a name="connect-sensors-by-using-tunneling"></a>Connettere i sensori tramite tunneling

Abilitare una connessione di tunneling protetta tra sensori aziendali e la console di gestione locale. Questa configurazione elude l'interazione con il firewall aziendale e, di conseguenza, riduce la superficie di attacco.

L'uso del tunneling consente di connettersi alla console di gestione locale dal relativo indirizzo IP e da un'unica porta, ovvero 9000, a qualsiasi sensore.

Per configurare il tunneling nella console di gestione locale:

- Accedere alla console di gestione locale ed eseguire i comandi seguenti:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Per configurare il tunneling sul sensore:

1. Aprire la porta TCP 9000 manualmente sul sensore (Network. Properties). Se la porta non è aperta, il sensore rifiuterà la connessione dalla console di gestione locale.

2. Accedere a ogni sensore ed eseguire i comandi seguenti:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Configurare un sito

La mappa aziendale predefinita fornisce una visualizzazione complessiva degli asset in base a diversi livelli di posizioni geografiche.

La visualizzazione degli asset potrebbe essere necessaria laddove la struttura organizzativa e le autorizzazioni utente sono complesse. In questi casi, l'installazione del sito potrebbe essere determinata da una struttura organizzativa globale, oltre alla struttura standard del sito o della zona.

Per supportare questo ambiente, è necessario creare una topologia aziendale globale basata sulle business unit, le aree, i siti e le zone aziendali dell'organizzazione. È anche necessario definire le autorizzazioni di accesso utente per queste entità usando i gruppi di accesso.

I gruppi di accesso consentono un migliore controllo sugli elementi in cui gli utenti gestiscono e analizzano gli asset nella piattaforma Defender per l'it.

### <a name="how-it-works"></a>Funzionamento

Per ogni sito è possibile definire una business unit e una regione. È quindi possibile aggiungere zone che sono entità logiche nella rete. 

Per ogni zona è necessario assegnare almeno un sensore. Il modello a cinque livelli offre la flessibilità e la granularità necessarie per fornire il sistema di protezione che riflette la struttura dell'organizzazione.

È possibile modificare i siti direttamente da qualsiasi vista mappa. Quando si apre un sito da una visualizzazione mappa, il numero di avvisi aperti viene visualizzato accanto a ogni zona.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Screenshot di una mappa della console di gestione locale con sovrapposizione dati di Berlino.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagramma che mostra i sensori e la relazione regionale.":::

Per configurare un sito:

1. Aggiungere nuove Business Unit per riflettere la struttura logica dell'organizzazione.

2. Aggiungere nuove aree per riflettere le aree dell'organizzazione.

3. Aggiungere un sito.

4. Aggiungere zone a un sito.

5. Connettere i sensori.

6. Assegnare il sensore alle zone del sito.

Per aggiungere le unità aziendali:

1. Dalla Vista Enterprise selezionare **tutti i siti**  >  **Gestisci business unit**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Screenshot che mostra la visualizzazione Gestisci business unit.":::

2. Immettere il nome della nuova business unit e selezionare **Aggiungi**.

Per aggiungere una nuova area:

1. Dalla Vista Enterprise selezionare **tutte le aree**  >  **Gestisci aree**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Screenshot che mostra la visualizzazione Gestisci aree.":::

2. Immettere il nome della nuova area e selezionare **Aggiungi**.

Per aggiungere un nuovo sito:

1. Nella visualizzazione Enterprise selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: nella barra superiore. Il cursore viene visualizzato come segno più ( **+** ).

2. Posizionare la **+** nella posizione del nuovo sito e selezionarla. Verrà visualizzata la finestra di dialogo **Crea nuovo sito** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Screenshot della visualizzazione Crea nuovo sito.":::

3. Definire il nome e l'indirizzo fisico per il nuovo sito e selezionare **Salva**. Il nuovo sito verrà visualizzato nella mappa del sito.

Per eliminare un sito:

1. Nella finestra di **gestione del sito** selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dalla barra che contiene il nome del sito, quindi selezionare **Elimina sito**. Viene visualizzata la casella conferma per verificare che si voglia eliminare il sito.

2. Nella casella di conferma, selezionare **Sì**. La casella di conferma si chiude e la finestra di **gestione del sito** viene visualizzata senza il sito eliminato.

## <a name="create-enterprise-zones"></a>Creare zone aziendali

Le zone sono entità logiche che consentono di dividere le risorse all'interno di un sito in gruppi in base a diverse caratteristiche. È ad esempio possibile creare gruppi per le linee di produzione, le sottostazioni, le aree del sito o i tipi di asset. È possibile definire le zone in base a qualsiasi caratteristica adatta alla propria organizzazione.

Le zone vengono configurate come parte del processo di configurazione del sito.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Screenshot della visualizzazione zone di gestione del sito.":::

Nella tabella seguente vengono descritti i parametri della finestra di **gestione del sito** .

| Parametro | Descrizione |
|--|--|
| Nome | Nome del sensore. È possibile modificare questo nome solo dal sensore. Per ulteriori informazioni, vedere la guida per l'utente di Defender for Internet. |
| IP | Indirizzo IP del sensore. |
| Versione | Versione del sensore. |
| Connettività | Stato di connettività del sensore. Lo stato può essere **connesso** o **disconnesso**. |
| Ultimo aggiornamento | Data dell'ultimo aggiornamento. |
| Stato aggiornamento | L'indicatore di stato Mostra lo stato del processo di aggiornamento, come indicato di seguito:<br />-Caricamento del pacchetto<br />-Preparazione dell'installazione<br />-Arresto dei processi<br />-Backup dei dati<br />-Creazione dello snapshot<br />-Aggiornamento della configurazione<br />-Aggiornamento delle dipendenze<br />-Aggiornamento delle librerie<br />-Applicazione di patch ai database<br />-Avvio dei processi<br />-Convalida dello stato di integrità del sistema<br />-Convalida riuscita<br />-Operazione riuscita<br />-Errore<br />-Aggiornamento avviato<br />-Avvio dell'installazioneogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Per informazioni dettagliate sull'aggiornamento, vedere [supporto tecnico Microsoft](https://support.microsoft.com/) per assistenza. |
| Asset | Il numero di asset OT monitorati dal sensore. |
| Avvisi | Numero di avvisi sul sensore. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Consente di assegnare un sensore alle zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Consente di eliminare un sensore disconnesso dal sito. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Indica il numero di sensori attualmente connessi alla zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Indica il numero di asset OT attualmente connessi alla zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Indica il numero di avvisi inviati dai sensori assegnati alla zona. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Annulla l'assegnazione dei sensori dalle zone. |

Per aggiungere una zona a un sito:

1. Nella finestra di **gestione del sito** selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dalla barra che contiene il nome del sito, quindi selezionare **Aggiungi zona**. Verrà visualizzata la finestra di dialogo **Crea nuova zona** .

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Screenshot della visualizzazione Crea nuova zona.":::

2. Immettere il nome della zona.

3. Immettere una descrizione per la nuova zona che indica chiaramente le caratteristiche usate per dividere il sito in zone.

4. Selezionare **SAVE** (SALVA). La nuova zona viene visualizzata nella finestra di **gestione del sito** sotto il sito a cui appartiene questa zona.

Per modificare una zona:

1. Nella finestra di **gestione del sito** selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dalla barra che contiene il nome della zona e quindi selezionare **modifica zona**. Verrà visualizzata la finestra di dialogo **modifica zona** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Screenshot che mostra la finestra di dialogo Modifica zona.":::

2. Modificare i parametri della zona e selezionare **Salva**.

Per eliminare una zona:

1. Nella finestra di **gestione del sito** selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dalla barra che contiene il nome della zona e quindi selezionare **Elimina zona**.

2. Nella casella di conferma, selezionare **Sì**.

Per filtrare in base allo stato di connettività:

- Nell'angolo in alto a sinistra selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: accanto a **connettività**, quindi selezionare una delle opzioni seguenti:

  - **All**: presenta tutti i sensori che fanno rapporto a questa console di gestione locale.

  - **Connected**: presenta solo sensori connessi.

  - **Disconnected**: presenta solo sensori disconnessi.

Per filtrare in base allo stato di aggiornamento:

- Nell'angolo in alto a sinistra selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: accanto a **stato aggiornamento** e selezionare una delle opzioni seguenti:

  - **All**: presenta tutti i sensori che fanno rapporto a questa console di gestione locale.

  - **Valido**: Visualizza i sensori con uno stato di aggiornamento valido.

  - **In corso**: presenta i sensori in corso di aggiornamento.

  - **Failed**: presenta sensori il cui processo di aggiornamento non è riuscito.

## <a name="assign-sensors-to-zones"></a>Assegnare sensori alle zone

Per ogni zona è necessario assegnare i sensori che eseguono l'analisi del traffico locale e gli avvisi. È possibile assegnare solo i sensori connessi alla console di gestione locale.

Per assegnare un sensore:

1. Selezionare **Gestione sito**. I sensori non assegnati vengono visualizzati nell'angolo superiore sinistro della finestra di dialogo.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Screenshot della visualizzazione dei sensori non assegnati.":::

2. Verificare che lo stato di **connettività** sia connesso. In caso contrario, vedere [connettere i sensori alla console di gestione locale](#connect-sensors-to-the-on-premises-management-console) per informazioni dettagliate sulla connessione. 

3. Selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: per il sensore che si vuole assegnare.

4. Nella finestra di dialogo **assegna sensore** selezionare la business unit, l'area, il sito e la zona da assegnare.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Screenshot della visualizzazione assegna sensore.":::

5. Selezionare **assegna**.

Per annullare l'assegnazione ed eliminare un sensore:

1. Disconnettere il sensore dalla console di gestione locale. Per informazioni dettagliate, vedere [connettere i sensori alla console di gestione locale](#connect-sensors-to-the-on-premises-management-console) .

2. Nella finestra di **gestione del sito** selezionare il sensore e selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . Il sensore viene visualizzato nell'elenco dei sensori non assegnati dopo alcuni istanti.

3. Per eliminare il sensore non assegnato dal sito, selezionare il sensore dall'elenco dei sensori non assegnati e selezionare :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="see-also"></a>Vedere anche

[Risolvere i problemi relativi al sensore e alla console di gestione locale](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
