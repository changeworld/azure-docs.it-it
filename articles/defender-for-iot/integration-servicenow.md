---
title: Informazioni sull'integrazione di ServiceNow
description: Il Defender per l'applicazione di gestione ICS di Internet per ServiceNow offre agli analisti SOC una visibilità multidimensionale nei protocolli OT specializzati e nei dispositivi Internet in ambienti industriali, oltre a analisi comportamentali compatibili con ICS per rilevare rapidamente comportamenti sospetti o anomali.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786006"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>Il Defender per l'applicazione di gestione ICS per ServiceNow

Il Defender per l'applicazione di gestione ICS di Internet per ServiceNow offre agli analisti SOC una visibilità multidimensionale nei protocolli OT specializzati e nei dispositivi Internet in ambienti industriali, oltre a analisi comportamentali compatibili con ICS per rilevare rapidamente comportamenti sospetti o anomali. Si tratta di un'evoluzione importante in base alla convergenza continua e all'OT per supportare le nuove iniziative di interesse, ad esempio le smart machine e l'intelligence in tempo reale.

L'applicazione Abilita anche l'IT e la risposta agli eventi imprevisti dall'interno di un SOC aziendale.

## <a name="about-defender-for-iot"></a>Informazioni su Defender per l'it

Defender per le cose offre l'unica piattaforma ICS e Cybersecurity creata da esperti del team blu con un track record che difende l'infrastruttura nazionale critica e l'unica piattaforma con analisi delle minacce e Machine Learning brevettate compatibili con ICS. Defender per l'it fornisce:

- Informazioni immediate su ICS il paesaggio del dispositivo con una vasta gamma di dettagli sugli attributi.

- Conoscenza incorporate approfondita con compatibilità con ICS di protocolli, dispositivi, applicazioni e comportamenti.

- Informazioni immediate sulle vulnerabilità e minacce note di zero giorni.

- Una tecnologia di modellazione delle minacce ICS automatizzata per prevedere i percorsi più probabili degli attacchi ICS mirati tramite l'analisi proprietaria.

> [!Note]
> I riferimenti a CyberX fanno riferimento ad Azure Defender per l'it.

## <a name="about-the-integration"></a>Informazioni sull'integrazione

Il Defender per l'integrazione con ServiceNow offre un nuovo livello di visibilità, monitoraggio e controllo centralizzato per le cose e il panorama. Queste piattaforme con bridging consentono la visibilità automatica dei dispositivi e la gestione delle minacce per i dispositivi ICS non raggiungibili in precedenza &.

### <a name="threat-management"></a>Gestione delle minacce

Il Defender per l'applicazione di gestione ICS Internet facilita le operazioni seguenti:

- Riduci il tempo necessario per le organizzazioni di infrastruttura industriali e critiche per rilevare, analizzare e agire sulle minacce informatiche.

- Ottenere informazioni in tempo reale sui rischi di OT.

- Correlare Defender per gli avvisi Internet con ServiceNow Threat Monitoring e flussi di lavoro di gestione degli eventi imprevisti.

- Attivare i ticket e i flussi di lavoro ServiceNow con altri servizi e applicazioni nella piattaforma ServiceNow.

Le minacce per la sicurezza ICS e SCADA sono identificate da Defender per i motori di sicurezza di Internet delle cose, che forniscono risposta immediata agli attacchi di malware, alla rete e alle deviazioni dei criteri di sicurezza, nonché alle anomalie operative e del protocollo. Per informazioni dettagliate sui dettagli degli avvisi inviati a ServiceNow, vedere [segnalazione avvisi](#alert-reporting).

### <a name="device-visibility-and-management"></a>Visibilità e gestione dei dispositivi

Il database di gestione della configurazione di ServiceNow (CMDB) è arricchito e integrato con un set completo di attributi del dispositivo inseriti dal Defender per la piattaforma Internet delle cose. In questo modo si garantisce una visibilità completa e continua nel paesaggio del dispositivo e consente di monitorare e rispondere da un singolo riquadro di vetro. Per informazioni dettagliate sugli attributi del dispositivo inviati a ServiceNowSee, vedere [visualizzare Defender per i rilevamenti delle cose in ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Architettura e requisiti di sistema

L'articolo illustra:

- **Requisiti software**  
- **Architettura**

## <a name="software-requirements"></a>Requisiti software

- Versione di ServiceNow Service Management 3.0.2

- Defender per la patch 2.8.11.1 o versione successiva

> [!Note]
> Se si sta già lavorando con un Defender per l'integrazione di ServiceNow e di Internet e si esegue l'aggiornamento usando la console di gestione locale, i dati precedente ricevuti da Defender per i sensori per le cose devono essere cancellati da ServiceNow.

## <a name="architecture"></a>Architettura

### <a name="on-premises-management-console-architecture"></a>Architettura della console di gestione locale

La console di gestione locale fornisce un'origine unificata per tutte le informazioni sul dispositivo e sugli avvisi inviate a ServiceNow.

È possibile configurare una console di gestione locale per comunicare con un'istanza di ServiceNow. La console di gestione locale inserisce i dati del sensore nell'applicazione Defender per l'uso dell'API REST.

Se si configura il sistema in modo che funzioni con una console di gestione locale, disabilitare la sincronizzazione ServiceNow, le regole di inoltro e le configurazioni proxy nei sensori, se sono state configurate.

Queste configurazioni devono essere impostate per la console di gestione locale. Le istruzioni di configurazione sono descritte in questo articolo.

### <a name="sensor-architecture"></a>Architettura del sensore

Se si vuole configurare l'ambiente in modo da includere la comunicazione diretta tra sensori e ServiceNow, per ogni sensore definire la sincronizzazione ServiceNow, le regole di inoltro e la configurazione proxy (se è necessario un proxy).

Si consiglia di configurare l'integrazione usando la console di gestione locale per comunicare con ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Creare token di accesso in ServiceNow

Questo articolo descrive come creare un token di accesso in ServiceNow. Il token è necessario per comunicare con Defender per l'it.

Sono necessari l' **ID client** e il **segreto client** quando si crea il Defender per le regole di inoltri per le cose, che inoltrano le informazioni sugli avvisi a ServiceNow e quando si configura Defender for Internet per il push degli attributi del dispositivo alle tabelle ServiceNow.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Configurare Defender per la comunicazione con ServiceNow

Questo articolo descrive come configurare Defender per la comunicazione con ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Inviare il Defender per informazioni sugli avvisi

Questo articolo descrive come configurare Defender for Internet per l'invio di informazioni sugli avvisi alle tabelle ServiceNow. Per informazioni sui dati degli avvisi inviati, vedere [segnalazione avvisi](#alert-reporting).

I difensori per gli avvisi Internet vengono visualizzati in ServiceNow come eventi imprevisti di sicurezza.

Definire un Defender per la regola di *inoltri* per l'invio di informazioni sugli avvisi a ServiceNow.

Per definire la regola:

1. Nel riquadro a sinistra di Defender for Internet, selezionare **inoltri**.  

1. Selezionare il :::image type="content" source="media/integration-servicenow/plus.png" alt-text="pulsante con l'icona più."::: . Verrà visualizzata la finestra di dialogo Crea regola di inoltri.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Crea regola di invio":::

1. Aggiungere un nome di regola.

1. Definire i criteri in base ai quali Defender per l'it attiverà la regola di invio. L'uso dei criteri per le regole di invio consente di individuare e gestire il volume delle informazioni inviate dal Defender per gli ServiceNow. Sono disponibili le opzioni seguenti:

    - **Livelli di gravità:** Si tratta dell'evento imprevisto minimo per il livello di sicurezza da inviare. Se, ad esempio, è selezionato **minor** , gli avvisi secondari e gli eventuali avvisi al di sopra di tale livello di gravità verranno trasmessi. I livelli sono predefiniti da Defender per l'it.

    - **Protocolli:** Attivare la regola di invio solo se il traffico rilevato era in esecuzione su protocolli specifici. Selezionare i protocolli richiesti dall'elenco a discesa oppure sceglierli tutti.

    - **Motori:** Selezionare i motori necessari o sceglierli tutti. Verranno inviati gli avvisi dei motori selezionati.

1. Verificare che sia selezionata l'opzione **segnala notifiche di avviso** .

1. Nella sezione azioni selezionare **Aggiungi** e quindi selezionare **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Selezionare ServiceNow dalle opzioni a discesa.":::

1. Immettere i parametri dell'azione ServiceNow:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Immettere i parametri dell'azione ServiceNow":::

1. Nel riquadro **azioni** impostare i parametri seguenti:

  | Parametro | Descrizione |
  |--|--|
  | Dominio | Immettere l'indirizzo IP del server ServiceNow. |
  | Username | Immettere il nome utente del server ServiceNow. |
  | Password | Immettere la password del server ServiceNow. |
  | ID client | Immettere l'ID client ricevuto per Defender per le cose nella pagina **registri applicazioni** in ServiceNow. |
  | Client Secret | Immettere la stringa del segreto client creata per Defender for all nella pagina **registri applicazioni** in ServiceNow. |
  | Tipo di report | **Eventi imprevisti**: inviare un elenco di avvisi presentati in ServiceNow con un ID evento imprevisto e una breve descrizione di ogni avviso.<br /><br />**Defender per l'applicazione** Internet: Inviare informazioni complete sugli avvisi, inclusi i dettagli del sensore, il motore, l'origine e gli indirizzi di destinazione. Le informazioni vengono trasmesse al difensore per le cose nell'applicazione ServiceNow. |

1. Selezionare **SAVE** (SALVA). I difensori per gli avvisi Internet vengono visualizzati come eventi imprevisti in ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Inviare il Defender per gli attributi dei dispositivi

Questo articolo descrive come configurare Defender for Internet per il push di una vasta gamma di attributi del dispositivo alle tabelle ServiceNow. Per informazioni dettagliate sul tipo di informazioni di cui è stato eseguito il push in ServiceNow, vedere ***informazioni di inventario*** .

Per inviare attributi a ServiceNow, è necessario eseguire il mapping della console di gestione locale a un'istanza di ServiceNow. Ciò garantisce che il Defender per la piattaforma Internet sia in grado di comunicare ed eseguire l'autenticazione con l'istanza.

Per aggiungere un'istanza di ServiceNow:

1. Accedere al Defender per la console di gestione locale.

1. Selezionare **impostazioni di sistema** e quindi **ServiceNow** nella sezione integrazione della console di gestione locale.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Selezionare il pulsante ServiceNow.":::

1. Immettere i seguenti parametri di sincronizzazione nella finestra di dialogo sincronizzazione ServiceNow.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="La finestra di dialogo ServiceNow Sync.":::

     Parametro | Descrizione |
    |--|--|
    | Abilita sincronizzazione | Abilitare e disabilitare la sincronizzazione dopo la definizione dei parametri. |
    | Frequenza di sincronizzazione (minuti) | Per impostazione predefinita, viene eseguito il push delle informazioni a ServiceNow ogni 60 minuti. Il valore minimo è 5 minuti. |
    | Istanza di ServiceNow | Immettere l'URL dell'istanza di ServiceNow. |
    | ID client | Immettere l'ID client ricevuto per Defender per le cose nella pagina **registri applicazioni** in ServiceNow. |
    | Client Secret | Immettere la stringa del segreto client creata per Defender for all nella pagina **registri applicazioni** in ServiceNow. |
    | Username | Immettere il nome utente per questa istanza. |
    | Password | Immettere la password per questa istanza. |

1. Selezionare **SAVE** (SALVA).

## <a name="verify-communication"></a>Verifica comunicazione

Verificare che la console di gestione locale sia connessa all'istanza di ServiceNow riesaminando la data dell' *ultima sincronizzazione* .

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Verificare la comunicazione che si è verificata esaminando l'ultima sincronizzazione.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Configurare le integrazioni usando il proxy HTTPS

Quando si configura il Defender per l'integrazione Internet e ServiceNow, la console di gestione locale e il server ServiceNow comunicano con la porta 443. Se il server ServiceNow è dietro il proxy, non è possibile usare la porta predefinita.

Defender for Internet delle cose supporta un proxy HTTPS nell'integrazione ServiceNow abilitando la modifica della porta predefinita usata per l'integrazione.

Per configurare il proxy:

1. Modificare le proprietà globali nella console di gestione locale:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Aggiungere i parametri seguenti:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Salvare e uscire.

4. Eseguire il comando seguente: `sudo monit restart all`

Dopo la configurazione, tutti i dati di ServiceNow vengono trasmessi tramite il proxy configurato.

## <a name="download-the-defender-for-iot-application"></a>Scarica l'applicazione Defender for Internet

Questo articolo descrive come scaricare l'applicazione.

Per accedere all'applicazione Defender for Internet:

1. Passare a <https://store.servicenow.com/>.

2. Cercare `Defender for IoT` o `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Cercare CyberX nella barra di ricerca.":::

3. Selezionare l'applicazione.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Selezionare l'applicazione nell'elenco.":::

4. Selezionare **Richiedi app.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Accedere all'applicazione con le proprie credenziali.":::

5. Accedere e scaricare l'applicazione.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Visualizza il Defender per i rilevamenti delle cose in ServiceNow

Questo articolo descrive gli attributi del dispositivo e le informazioni sugli avvisi presentati in ServiceNow.

Per visualizzare gli attributi del dispositivo:

1. Accedere a ServiceNow.

2. Passare alla **piattaforma CyberX**.

3. Passare a **inventario** o **avviso**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventario o avviso":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Informazioni inventario

Il database di gestione della configurazione (CMDB) è arricchito ed è integrato da dati inviati da Defender per gli ServiceNow. Con l'aggiunta o l'aggiornamento degli attributi del dispositivo nelle tabelle degli elementi di configurazione CMDB di ServiceNow, Defender for Internet è in grado di attivare i flussi di lavoro e le regole di business ServiceNow.

Sono disponibili le informazioni seguenti:

- Attributi del dispositivo, ad esempio il MAC, il sistema operativo, il fornitore o il protocollo del dispositivo rilevato.

- Informazioni sul firmware, ad esempio la versione del firmware e il numero di serie.

- Informazioni sul dispositivo connesso, ad esempio la direzione del traffico tra l'origine e la destinazione.

### <a name="devices-attributes"></a>Attributi dispositivi

Questo articolo descrive gli attributi del dispositivo di cui è stato eseguito il push in ServiceNow.

| Elemento | Descrizione |
|--|--|
| Appliance | Nome del sensore che ha rilevato il traffico. |
| ID | ID dispositivo assegnato da Defender per l'it. |
| Nome | Nome del dispositivo. |
| Indirizzo IP | Indirizzo o indirizzi IP del dispositivo. |
| Tipo | Tipo di dispositivo, ad esempio switch, PLC, storico o controller di dominio. |
| Indirizzo MAC | Indirizzo o indirizzi MAC del dispositivo. |
| Sistema operativo | Sistema operativo del dispositivo. |
| Vendor | Fornitore del dispositivo. |
| Protocolli | Protocolli rilevati nel traffico generato dal dispositivo. |
| Proprietario | Immettere il nome del proprietario del dispositivo. |
| Location | Immettere la posizione fisica del dispositivo. |

Visualizza i dispositivi connessi a un dispositivo in questa visualizzazione.

Per visualizzare i dispositivi connessi:

1. Selezionare un dispositivo e quindi selezionare l' **Appliance** elencato in per il dispositivo.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Selezionare l'appliance desiderata dall'elenco.":::

1. Nella finestra di dialogo **Dettagli dispositivo** selezionare **dispositivi connessi**.

### <a name="firmware-details"></a>Dettagli del firmware

Questo articolo descrive le informazioni sul firmware del dispositivo inviate a ServiceNow.

| Elemento | Descrizione |
|--|--|
| Appliance | Nome del sensore che ha rilevato il traffico. |
| Dispositivo | Nome del dispositivo. |
| Indirizzo | Indirizzo IP del dispositivo. |
| Indirizzo modulo | Il modello e il numero di slot o l'ID del dispositivo. |
| Serial | Numero di serie del dispositivo. |
| Modello | Numero del modello del dispositivo. |
| Versione | Numero di versione del firmware. |
| Dati aggiuntivi | Più dati sul firmware come definito dal fornitore, ad esempio il tipo di dispositivo. |

### <a name="connection-details"></a>Dettagli connessione

Questo articolo descrive le informazioni di connessione del dispositivo inviate a ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Informazioni di connessione del dispositivo":::

| Elemento | Descrizione |
|--|--|
| Appliance | Nome del sensore che ha rilevato il traffico. |
| Direzione | Direzione del traffico. <br /> <br /> - **Un modo** indica che la destinazione è il server e l'origine è il client. <br /> <br /> - **Due modalità** indicano che l'origine e la destinazione sono server o che il client è sconosciuto. |
| ID dispositivo di origine | Indirizzo IP del dispositivo che ha comunicato con il dispositivo connesso. |
| Nome dispositivo di origine | Nome del dispositivo che ha comunicato con il dispositivo connesso. |
| ID dispositivo di destinazione | Indirizzo IP del dispositivo connesso. |
| Nome dispositivo di destinazione | Nome del dispositivo connesso. |

## <a name="alert-reporting"></a>Segnalazione avvisi

Gli avvisi vengono attivati quando i difensori dei motori delle cose rilevano le modifiche nel traffico di rete e il comportamento che richiedono attenzione. Per informazioni dettagliate sui tipi di avvisi generati da ogni motore, vedere [informazioni sui motori di avvisi](#about-alert-engines).

Questo articolo descrive le informazioni sugli avvisi del dispositivo inviate a ServiceNow.

| Elemento | Descrizione |
|--|--|
| Data di creazione | Data e ora di generazione dell'avviso. |
| Motore | Motore che ha rilevato l'evento. |
| Titolo | Titolo dell'avviso. |
| Descrizione | Descrizione dell'avviso. |
| Protocollo | Protocollo rilevato nel traffico. |
| Gravità | Gravità dell'avviso definita da Defender per l'it. |
| Appliance | Nome del sensore che ha rilevato il traffico. |
| Nome origine | Nome dell'origine. |
| Indirizzo IP di origine| Indirizzo IP di origine. |
| Nome destinazione | Nome della destinazione. |
| Indirizzo IP di destinazione | Indirizzo IP di destinazione. |
| Assegnatario | Immettere il nome dell'utente assegnato al ticket. |

### <a name="updating-alert-information"></a>Aggiornamento delle informazioni sugli avvisi

Selezionare la voce nella colonna creato per visualizzare le informazioni sugli avvisi in un modulo. È possibile aggiornare i dettagli dell'avviso e assegnare l'avviso a un singolo utente da rivedere e gestire.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Visualizzare le informazioni dell'avviso.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Informazioni sui motori di avvisi

Questo articolo descrive il tipo di avvisi attivati da ogni motore.

| Tipo di avviso | Descrizione |
|--|--|
| Avvisi di violazione dei criteri | Attivato quando il motore di violazione dei criteri rileva una deviazione dal traffico appreso in precedenza. Ad esempio: <br /><br />-Viene rilevato un nuovo dispositivo. <br /><br />-Viene rilevata una nuova configurazione in un dispositivo. <br /><br />-Un dispositivo non definito come dispositivo di programmazione comporta una modifica della programmazione. <br /><br />-È stata modificata una versione del firmware. |
| Avvisi di violazione del protocollo | Attivato quando il motore di violazione del protocollo rileva le strutture di pacchetti o i valori dei campi che non sono conformi alla specifica del protocollo. |
| Avvisi operativi | Attivato quando il motore operativo rileva problemi operativi di rete o malfunzionamenti del dispositivo. Ad esempio, un dispositivo di rete è stato interrotto utilizzando un comando Arresta PLC oppure un'interfaccia su un sensore ha interrotto il monitoraggio del traffico. |
| Avvisi malware | Attivato quando il motore malware rileva attività di rete dannosa, ad esempio, attacchi noti come Conficker. |
| Avvisi di anomalie | Attivato quando il motore delle anomalie rileva una deviazione. Ad esempio, un dispositivo esegue l'analisi di rete, ma non è definito come dispositivo di analisi. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [inviare le informazioni sugli avvisi](how-to-forward-alert-information-to-partners.md).
