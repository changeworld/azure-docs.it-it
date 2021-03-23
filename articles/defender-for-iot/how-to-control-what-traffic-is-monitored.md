---
title: Controllare quale traffico viene monitorato
description: I sensori eseguono automaticamente il rilevamento approfondito dei pacchetti per l'IT e il traffico e per risolvere le informazioni sui dispositivi di rete, ad esempio gli attributi e il comportamento della rete. Sono disponibili diversi strumenti per controllare il tipo di traffico rilevato da ogni sensore.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784442"
---
# <a name="control-what-traffic-is-monitored"></a>Controllare quale traffico viene monitorato

I sensori eseguono automaticamente il rilevamento approfondito dei pacchetti per l'IT e il traffico e per risolvere le informazioni sui dispositivi di rete, ad esempio gli attributi e il comportamento del dispositivo. Sono disponibili diversi strumenti per controllare il tipo di traffico rilevato da ogni sensore.

## <a name="learning-and-smart-it-learning-modes"></a>Apprendimento e modalità di apprendimento IT intelligenti

La modalità di apprendimento indica al sensore di apprendere le normali attività della rete. Gli esempi sono i dispositivi individuati nella rete, i protocolli rilevati nella rete, i trasferimenti di file tra dispositivi specifici e altro ancora. Questa attività diventa la linea di base di rete.

La modalità di apprendimento viene abilitata automaticamente dopo l'installazione e rimarrà abilitata fino alla disattivazione. Il periodo di apprendimento approssimativo è compreso tra due o sei settimane, a seconda delle dimensioni e della complessità della rete. Dopo questo periodo, quando la modalità di apprendimento è disabilitata, qualsiasi nuova attività rilevata attiverà gli avvisi. Gli avvisi vengono attivati quando il motore dei criteri individua le deviazioni dalla baseline acquisita.

Una volta completato il periodo di apprendimento e la modalità di apprendimento è disabilitata, il sensore potrebbe rilevare un livello insolitamente elevato di modifiche di base che sono il risultato della normale attività IT, ad esempio le richieste DNS e HTTP. L'attività è denominata comportamento IT non deterministico. Il comportamento potrebbe inoltre attivare avvisi di violazione dei criteri non necessari e notifiche di sistema. Per ridurre la quantità di avvisi e notifiche, è possibile abilitare la funzione di **apprendimento intelligente it** .

Quando è abilitata l'apprendimento intelligente IT, il sensore tiene traccia del traffico di rete che genera comportamenti IT non deterministici in base a scenari di avviso specifici.

Il sensore monitora il traffico per sette giorni. Se rileva lo stesso traffico IT non deterministico entro sette giorni, continuerà a monitorare il traffico per altri sette giorni. Quando il traffico non viene rilevato per sette giorni, l'apprendimento intelligente è disabilitato per tale scenario. Il nuovo traffico rilevato per tale scenario genererà solo avvisi e notifiche.

L'uso di Smart IT Learning consente di ridurre il numero di avvisi e notifiche non necessari causati da scenari IT rumorosi.

Se il sensore è controllato dalla console di gestione locale, non è possibile disabilitare le modalità di apprendimento. In casi come questo, è possibile disabilitare la modalità di apprendimento solo dalla console di gestione.

Le funzionalità di apprendimento (apprendimento e apprendimento IT intelligenti) sono abilitate per impostazione predefinita.

Per abilitare o disabilitare l'apprendimento:

- Selezionare **impostazioni di sistema** e impostare le **Opzioni di apprendimento e** **apprendimento it intelligenti** .

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Schermata di attivazione/disimpostazione impostazioni di sistema.":::

## <a name="configure-subnets"></a>Configurare le subnet

Le configurazioni delle subnet influiscono sulla modalità di visualizzazione dei dispositivi nella mappa del dispositivo.

Per impostazione predefinita, il sensore individua la configurazione della subnet e popola la finestra di dialogo **configurazione subnet** con queste informazioni.

Per abilitare lo stato attivo sui dispositivi OT, i dispositivi IT vengono aggregati automaticamente per subnet nella mappa del dispositivo. Ogni subnet viene presentata come una singola entità sulla mappa, inclusa una funzionalità di compressione/espansione interattiva per eseguire il drill-down in una subnet IT e viceversa.

Quando si usano le subnet, selezionare le subnet ICS per identificare le subnet OT. È quindi possibile concentrare la visualizzazione mappa sulle reti OT e ICS e comprimere almeno la presentazione degli elementi di rete IT. Questa operazione riduce il numero totale di dispositivi visualizzati sulla mappa e fornisce un quadro chiaro degli elementi di rete OT e ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Screenshot che mostra l'applicazione di filtri a una rete.":::

È possibile modificare la configurazione o modificare le informazioni sulla subnet manualmente esportando i dati individuati, cambiandoli manualmente e quindi importando di nuovo l'elenco di subnet definite manualmente. Per ulteriori informazioni sull'esportazione e l'importazione, vedere [Import Device Information](how-to-import-device-information.md).

In alcuni casi, ad esempio gli ambienti che usano intervalli pubblici come intervalli interni, è possibile istruire il sensore per la risoluzione di tutte le subnet come subnet interne selezionando l'opzione **non rilevare l'attività Internet** . Quando si seleziona questa opzione:

- Gli indirizzi IP pubblici verranno considerati come indirizzi locali.

- Non verrà inviato alcun avviso sull'attività Internet non autorizzata, che riduce le notifiche e gli avvisi ricevuti sugli indirizzi esterni.

Per configurare le subnet:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Nella finestra **Impostazioni sistema** selezionare **Subnet**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Screenshot che mostra la schermata di configurazione della subnet."::: 

3. Per aggiungere automaticamente le subnet quando vengono individuati nuovi dispositivi, è necessario aggiornare le **subnet automatiche** .

4. Per risolvere tutte le subnet come subnet interne, selezionare **non rilevare l'attività Internet**.

5. Selezionare **Aggiungi rete** e definire i parametri seguenti per ogni subnet:

    - Indirizzo IP della subnet.
    - Indirizzo del subnet mask.
    - Nome della subnet. Si consiglia di denominare ogni subnet con un nome significativo che è possibile identificare facilmente, in modo da poter distinguere tra le reti IT e OT. Il nome può essere composto da un massimo di 60 caratteri.

6. Per contrassegnare questa subnet come subnet OT, selezionare la **subnet ICS**.

7. Per presentare separatamente la subnet quando si sta organizzando la mappa in base al livello di Purdue, selezionare **segregato**.

8. Per eliminare una subnet, selezionare :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Per eliminare tutte le subnet, selezionare **Clear All (Cancella tutto**).

10. Per esportare le subnet configurate, selezionare **Esporta**. La tabella subnet viene scaricata nella workstation.

11. Selezionare **Salva**.

### <a name="importing-information"></a>Importazione di informazioni 

Per importare le informazioni sulla subnet, selezionare **Importa** e selezionare un file CSV da importare. Le informazioni sulla subnet vengono aggiornate con le informazioni importate. Se è importante un campo vuoto, i dati andranno persi.

## <a name="detection-engines"></a>Motori di rilevamento

I motori di analisi self-learning eliminano la necessità di aggiornare le firme o definire regole. I motori usano analisi del comportamento specifiche di ICS e data science per analizzare continuamente il traffico di rete per anomalie, malware, problemi operativi, violazioni del protocollo e deviazioni delle attività di rete di base.

> [!NOTE]
> Si consiglia di abilitare tutti i motori di sicurezza.

Quando un motore rileva una deviazione, viene generato un avviso. È possibile visualizzare e gestire gli avvisi dalla schermata avvisi o da un sistema partner.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Screenshot che mostra il rilevamento della deviazione.":::

Il nome del motore che ha attivato l'avviso viene visualizzato sotto il titolo dell'avviso.

### <a name="protocol-violation-engine"></a>Motore di violazione del protocollo 

Una violazione del protocollo si verifica quando i valori del campo o della struttura del pacchetto non sono conformi alla specifica del protocollo.

Scenario di esempio: avviso *"operazione Modbus non valida (codice della funzione zero)"* . Questo avviso indica che un dispositivo primario ha inviato una richiesta con codice di funzione 0 a un dispositivo secondario. Questa azione non è consentita in base alla specifica del protocollo e il dispositivo secondario potrebbe non gestire correttamente l'input.

### <a name="policy-violation-engine"></a>Motore di violazione dei criteri

Si verifica una violazione dei criteri con una deviazione dal comportamento di base definito nelle impostazioni apprese o configurate.

Scenario di esempio: avviso *"agente utente http non autorizzato"* . Questo avviso indica che un'applicazione non appresa o approvata dai criteri viene usata come client HTTP in un dispositivo. Potrebbe trattarsi di un nuovo Web browser o di un'applicazione su tale dispositivo.

### <a name="malware-engine"></a>Motore malware

Il motore di malware rileva un'attività di rete dannosa.

Scenario di esempio: avviso *"sospetto di attività dannosa (Stuxnet)"* . Questo avviso indica che il sensore ha rilevato un'attività di rete sospetta nota come correlata al malware Stuxnet. Questo malware è una minaccia permanente avanzata destinata al controllo industriale e alle reti SCADA.

### <a name="anomaly-engine"></a>Motore anomalie

Il motore delle anomalie rileva le anomalie nel comportamento di rete.

Scenario di esempio: avviso *"comportamento periodico nel canale di comunicazione"* . Il componente esamina le connessioni di rete e rileva il comportamento periodico e ciclico della trasmissione dei dati. Questo comportamento è comune nelle reti industriali.

### <a name="operational-engine"></a>Motore operativo

Il motore operativo rileva gli eventi imprevisti operativi o le entità che non funzionano correttamente.

Scenario di esempio: avviso *"il dispositivo è sospettato di essere disconnesso (non risponde)"* . Questo avviso viene generato quando un dispositivo non risponde a un tipo di richiesta per un periodo predefinito. Questo avviso potrebbe indicare un arresto del dispositivo, la disconnessione o il malfunzionamento.

### <a name="enable-and-disable-engines"></a>Abilitare e disabilitare i motori

Quando si disabilita un motore dei criteri, le informazioni generate dal motore non saranno disponibili per il sensore. Se, ad esempio, si disabilita il motore delle anomalie, non si riceveranno avvisi sulle anomalie di rete. Se è stata creata una regola di inoltramento, le anomalie apprese dal motore non verranno inviate. Per abilitare o disabilitare un motore dei criteri, selezionare **abilitato** o **disabilitato** per il motore specifico.

Il Punteggio complessivo viene visualizzato nell'angolo in basso a destra della schermata **Impostazioni sistema** . Il Punteggio indica la percentuale di protezione disponibile abilitata tramite i motori di protezione dalle minacce. Ogni motore è il 20% della protezione disponibile.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Screenshot che mostra un punteggio.":::

## <a name="configure-dhcp-address-ranges"></a>Configurare gli intervalli di indirizzi DHCP

La rete potrebbe essere costituita da indirizzi IP statici e dinamici. In genere, gli indirizzi statici si trovano nelle reti OT attraverso gli storici, i controller e i dispositivi dell'infrastruttura di rete, ad esempio commutatori e router. L'allocazione di indirizzi IP dinamici viene in genere implementata nelle reti guest con laptop, PC, smartphone e altre apparecchiature portatili (usando connessioni fisiche Wi-Fi o LAN in posizioni diverse).

Se si usano reti dinamiche, si gestiscono le modifiche degli indirizzi IP che si verificano quando vengono assegnati nuovi indirizzi IP. A tale scopo, è necessario definire gli intervalli di indirizzi DHCP.

Potrebbero verificarsi modifiche, ad esempio quando un server DHCP assegna indirizzi IP.

La definizione di indirizzi IP dinamici in ogni sensore consente un supporto completo e trasparente nelle istanze delle modifiche degli indirizzi IP. Ciò garantisce la creazione di report completi per ogni dispositivo univoco.

La console dei sensori presenta l'indirizzo IP più attuale associato al dispositivo e indica i dispositivi dinamici. Ad esempio:

- Il report di data mining e l'inventario del dispositivo consolidano tutte le attività apprese dal dispositivo come un'entità, indipendentemente dalle modifiche all'indirizzo IP. Questi report indicano quali indirizzi sono stati definiti come indirizzi DHCP.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Screenshot che mostra l'inventario dei dispositivi.":::

- La finestra **Proprietà dispositivo** indica se il dispositivo è stato definito come dispositivo DHCP.

Per impostare un intervallo di indirizzi DHCP:

1.  Nel menu laterale selezionare **intervalli DHCP** dalla finestra impostazioni di **sistema** .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Screenshot che mostra la selezione degli intervalli DHCP.":::

2.  Definire un nuovo intervallo impostando i valori **da** e **a** .

3.  Facoltativamente: definire il nome dell'intervallo, con un massimo di 256 caratteri.

4.  Per esportare gli intervalli in un file CSV, selezionare **Esporta**.

5. Per aggiungere manualmente più intervalli da un file CSV, selezionare **Importa** , quindi selezionare il file.

    > [!NOTE]
    > Gli intervalli importati da un file CSV sovrascrivono le impostazioni dell'intervallo esistente.

6. Selezionare **Salva**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Configurare i server DNS per la risoluzione della ricerca inversa

Per migliorare l'arricchimento dei dispositivi, è possibile configurare più server DNS per eseguire ricerche inverse. È possibile risolvere i nomi host o FQDN associati agli indirizzi IP rilevati nelle subnet di rete. Se, ad esempio, un sensore individua un indirizzo IP, è possibile che venga eseguita una query su più server DNS per risolvere il nome host.

Sono supportati tutti i formati CIDR.

Il nome host viene visualizzato nell'inventario dei dispositivi, nella mappa dei dispositivi e nei report.

È possibile pianificare pianificazioni per la risoluzione della ricerca inversa per intervalli orari specifici, ad esempio ogni 12 ore. In alternativa, è possibile pianificare un determinato periodo di tempo.

Per definire i server DNS:

1. Selezionare **impostazioni di sistema** , quindi selezionare **impostazioni DNS**.

2. Selezionare **Aggiungi server DNS**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Screenshot che mostra la selezione di Aggiungi server DNS.":::

3. Nel campo **Pianifica ricerca DNS inverso** scegliere una delle seguenti operazioni:

    - Intervalli (all'ora).
  
    - Tempo specifico. Usare la formattazione europea. Ad esempio, usare **14:30** e non **2:30 PM**.

4. Nel campo **Indirizzo server DNS** immettere l'indirizzo IP DNS.

5. Nel campo **porta server DNS** immettere la porta DNS.

6. Risolvere gli indirizzi IP di rete in FQDN del dispositivo. Nel campo **Number of labels** aggiungere il numero di etichette di dominio da visualizzare. Da sinistra a destra vengono visualizzati fino a 30 caratteri.

7. Nel campo **subnet** immettere le subnet su cui si desidera eseguire la query sul server DNS.

8. Selezionare l'interruttore **Abilita** se si desidera avviare la ricerca inversa.

### <a name="test-the-dns-configuration"></a>Testare la configurazione DNS 

Usando un dispositivo di test, verificare che le impostazioni definite funzionino correttamente:

1. Abilitare l'interruttore per la **ricerca DNS** .

2. Selezionare **Test**.

3. Immettere un indirizzo in **indirizzo di ricerca** per la finestra di dialogo **test di ricerca inversa DNS per il server** .

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Screenshot che mostra l'area degli indirizzi di ricerca.":::

4. Selezionare **Test**.

## <a name="configure-windows-endpoint-monitoring"></a>Configurare il monitoraggio degli endpoint di Windows

Con la funzionalità di monitoraggio degli endpoint di Windows, è possibile configurare Azure Defender per la ricerca selettiva dei sistemi Windows. Questo consente di ottenere informazioni più mirate e accurate sui dispositivi, ad esempio i livelli Service Pack.

È possibile configurare il sondaggio con intervalli e host specifici e configurarlo in modo che venga eseguito solo con la frequenza desiderata. Per eseguire il probe selettivo, è possibile utilizzare il Strumentazione gestione Windows (WMI), ovvero il linguaggio di scripting standard di Microsoft per la gestione dei sistemi Windows.

> [!NOTE]
> - È possibile eseguire una sola analisi alla volta.
> - Si ottengono i migliori risultati con gli utenti che dispongono dei privilegi di amministratore di dominio o locale.
> - Prima di iniziare la configurazione di WMI, configurare una regola del firewall che apre il traffico in uscita dal sensore alla subnet analizzata usando la porta UDP 135 e tutte le porte TCP sopra 1024.

Al termine del probe, un file di log con tutti i tentativi di sondaggio è disponibile dall'opzione per esportare un log. Il log contiene tutti gli indirizzi IP di cui è stato rilevato il probe. Per ogni indirizzo IP, nel log vengono visualizzate le informazioni sull'esito positivo e negativo. È presente anche un codice di errore, ovvero una stringa gratuita derivata dall'eccezione. L'analisi dell'ultimo log viene mantenuta nel sistema.

È possibile eseguire analisi pianificate o analisi manuali. Al termine di un'analisi, è possibile visualizzare i risultati in un file CSV.

**Prerequisiti**

Configurare una regola del firewall che apre il traffico in uscita dal sensore alla subnet analizzata usando la porta UDP 135 e tutte le porte TCP sopra 1024.

Per configurare un'analisi automatica:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Selezionare **monitoraggio endpoint di Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot che mostra la selezione del monitoraggio degli endpoint di Windows.":::

3. Nel riquadro **pianificazione analisi** configurare le opzioni come segue:

      - **Per intervalli fissi (in ore)**: impostare la pianificazione dell'analisi in base a intervalli in ore.

      - **Per orari specifici**: impostare la pianificazione dell'analisi in base a orari specifici e selezionare **Salva analisi**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Screenshot che mostra il pulsante Salva analisi.":::

4. Per definire l'intervallo di analisi, selezionare **Imposta intervalli di analisi**.

5. Impostare l'intervallo di indirizzi IP e aggiungere l'utente e la password.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Screenshot che mostra l'aggiunta di un utente e una password.":::

6. Per escludere un intervallo di indirizzi IP da un'analisi, selezionare **Disabilita** accanto all'intervallo.

7. Per rimuovere un intervallo, selezionare :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: accanto all'intervallo.

8. Selezionare **Salva**. La finestra di dialogo **modifica configurazione intervalli di analisi** si chiude e il numero di intervalli viene visualizzato nel riquadro **intervalli di analisi** .

Per eseguire un'analisi manuale:

1. Nel menu laterale selezionare impostazioni di **sistema**.

2. Selezionare **monitoraggio endpoint di Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot che mostra la schermata di configurazione di monitoraggio endpoint di Windows.":::

3. Nel riquadro **azioni** selezionare **Avvia analisi**. Viene visualizzata una barra di stato nel riquadro **azioni** e viene visualizzato lo stato di avanzamento del processo di analisi.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Screenshot che mostra il pulsante Avvia analisi.":::

Per visualizzare i risultati dell'analisi:

1. Al termine dell'analisi, nel riquadro **azioni** selezionare **Visualizza risultati analisi**. Il file CSV con i risultati dell'analisi viene scaricato nel computer.

## <a name="see-also"></a>Vedi anche

[Esaminare i rilevamenti dei sensori nell'inventario](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 dei dispositivi [Esaminare i rilevamenti dei sensori nella mappa del dispositivo](how-to-work-with-the-sensor-device-map.md)
