---
title: Risolvere i problemi relativi al sensore e alla console di gestione locale
description: Risolvere i problemi relativi al sensore e alla console di gestione locale per eliminare eventuali problemi che potrebbero verificarsi.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: b91827fc0a6fb8380c9f8aa87a3def3bc1819523
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955434"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Risolvere i problemi relativi al sensore e alla console di gestione locale

Questo articolo descrive gli strumenti di base per la risoluzione dei problemi relativi al sensore e alla console di gestione locale. Oltre agli elementi descritti in questa sezione, è possibile verificare l'integrità del sistema nei modi seguenti:

**Avvisi**: viene creato un avviso quando l'interfaccia del sensore che monitora il traffico è inattiva. 

**SNMP**: lo stato del sensore viene monitorato tramite SNMP. Azure Defender per le cose Internet risponde alle query SNMP inviate da un server di monitoraggio autorizzato. 

**Notifiche di sistema**: quando una console di gestione controlla il sensore, è possibile inviare avvisi sui backup dei sensori non riusciti e sui sensori disconnessi.

## <a name="sensor-troubleshooting-tools"></a>Strumenti per la risoluzione dei problemi del sensore

### <a name="investigate-password-failure-at-initial-sign-in"></a>Esaminare gli errori di password all'accesso iniziale

Quando si accede a un sensore di freccia preconfigurato per la prima volta, è necessario eseguire il ripristino della password.

Per ripristinare la password:

1. Nella schermata Defender for Internet all Sign-in selezionare il  **ripristino della password**. Verrà visualizzata la schermata di **ripristino della password** .

1. Selezionare **CyberX** o **support**, quindi copiare l'identificatore univoco.

1. Passare alla portale di Azure e selezionare **siti e sensori**.  

1. Selezionare la scheda **Ripristina password della console di gestione locale** .

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Selezionare il pulsante Ripristina gestione locale per scaricare il file di ripristino.":::

1. Immettere l'identificatore univoco ricevuto nella schermata di **ripristino della password** e selezionare **Ripristina**. Il `password_recovery.zip` file viene scaricato.

    > [!NOTE]
    > Non modificare il file di recupero della password. Si tratta di un file firmato che non funziona se si manomette.

1. Nella schermata di **ripristino della password** selezionare **carica**. Viene visualizzata **la finestra Carica file di ripristino della password** .

1. Selezionare **Sfoglia** per individuare il `password_recovery.zip` file o trascinare la nella `password_recovery.zip` finestra.

1. Selezionare **Avanti**. verrà visualizzato l'utente e la password generata dal sistema per la console di gestione.

    > [!NOTE]
    > Quando si accede a un sensore o a una console di gestione locale per la prima volta, questo verrà collegato alla sottoscrizione a cui è stata connessa. Se è necessario reimpostare la password per il CyberX o l'utente del supporto, sarà necessario selezionare tale sottoscrizione. Per ulteriori informazioni sul ripristino di un CyberX o la password dell'utente di supporto, vedere [reimpostazione della password di un utente per il sensore o la console di gestione locale](how-to-create-and-manage-users.md#resetting-a-users-password-for-the-sensor-or-on-premises-management-console)

### <a name="investigate-a-lack-of-traffic"></a>Esaminare la mancanza di traffico

Un indicatore viene visualizzato nella parte superiore della console quando il sensore rileva che non è presente traffico su una delle porte configurate. Questo indicatore è visibile a tutti gli utenti.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Screenshot dell'avviso che non è stato rilevato traffico.":::
 
Quando viene visualizzato questo messaggio, è possibile esaminare la posizione in cui non è presente traffico. Verificare che il cavo span sia connesso e che non siano state apportate modifiche all'architettura span.  

Per informazioni sul supporto e sulla risoluzione dei problemi, contattare [supporto tecnico Microsoft](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>Controllare le prestazioni del sistema 

Quando viene distribuito un nuovo sensore o, ad esempio, il sensore funziona lentamente o non visualizza alcun avviso, è possibile controllare le prestazioni del sistema.

Per controllare le prestazioni del sistema:

1. Nel dashboard verificare che `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Screenshot di un dashboard di esempio."::: 

1. Dal menu laterale selezionare **Devices (dispositivi**).

1. Nella finestra **dispositivi** verificare che i dispositivi vengano individuati.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Assicurarsi che i dispositivi vengano individuati.":::

1. Scegliere **data mining** dal menu laterale.

1. Nella finestra **data mining** selezionare **tutti** e generare un report.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Consente di generare un nuovo report utilizzando data mining.":::

1. Verificare che il report contenga dati.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Verificare che il report contenga dati.":::

1. Dal menu laterale selezionare **trends & Statistics**.

1. Nella finestra **tendenze & statistiche** selezionare **Aggiungi widget**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Per aggiungere un widget, selezionarlo.":::

1. Aggiungere un widget e assicurarsi che mostri i dati.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Verificare che il widget mostri i dati.":::

1. Dal menu laterale selezionare **avvisi**. Verrà visualizzata la finestra **avvisi** .

1. Verificare che gli avvisi siano stati creati.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Verificare che gli avvisi siano stati creati.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Verificare la mancanza di avvisi previsti

Se nella finestra **avvisi** non viene visualizzato un avviso previsto, verificare quanto segue:

- Verificare che lo stesso avviso sia già presente nella finestra **avvisi** come risposta a un'altra istanza di sicurezza. Se sì, questo avviso non è ancora stato gestito, la console dei sensori non visualizza un nuovo avviso.

- Assicurarsi che questo avviso non sia stato escluso tramite le regole di **esclusione degli avvisi** nella console di gestione di. 

### <a name="investigate-widgets-that-show-no-data"></a>Esaminare i widget che non mostrano dati

Quando i widget nella finestra **tendenze & statistiche** non visualizzano dati, eseguire le operazioni seguenti:

- [Controllare le prestazioni del sistema](#check-system-performance).

- Verificare che le impostazioni relative a ora e area siano configurate correttamente e non siano impostate su un'ora futura. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Esaminare una mappa dei dispositivi che mostra solo i dispositivi broadcast

Quando i dispositivi visualizzati nella mappa non sono connessi tra loro, è possibile che si verifichi un errore con la configurazione della porta SPAN. Ovvero, è possibile che vengano visualizzati solo i dispositivi broadcast e nessun traffico unicast.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Visualizzare i dispositivi broadcast.":::

In tal caso, è necessario verificare che sia possibile visualizzare solo il traffico broadcast. Quindi, rivolgersi al tecnico di rete per correggere la configurazione della porta SPAN in modo da visualizzare il traffico unicast.

Per convalidare che venga visualizzato solo il traffico di trasmissione:

- Nella schermata **data mining** creare un report utilizzando l'opzione **All** . Verificare quindi se nel report viene visualizzato solo il traffico broadcast e multicast (e nessun traffico unicast).

Oppure:

- Registrare un PCAP direttamente dal Commuter o connettere un computer portatile usando Wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Connettere il sensore a NTP

È possibile configurare un sensore autonomo e una console di gestione, con i sensori correlati, per la connessione a NTP.

Per connettere un sensore autonomo a NTP:

- [Per assistenza, contattare il team di supporto](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

Per connettere un sensore controllato dalla console di gestione a NTP:

- La connessione a NTP è configurata nella console di gestione. Tutti i sensori controllati dalla console di gestione ottengono automaticamente la connessione NTP.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Verificare quando i dispositivi non vengono visualizzati sulla mappa o se sono presenti più avvisi relativi a Internet

A volte i dispositivi ICS sono configurati con indirizzi IP esterni. Questi dispositivi ICS non vengono visualizzati sulla mappa. Anziché i dispositivi, sulla mappa viene visualizzato un cloud Internet. Gli indirizzi IP di questi dispositivi sono inclusi nell'immagine cloud.

Un'altra indicazione dello stesso problema si verifica quando vengono visualizzati più avvisi correlati a Internet.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Più avvisi correlati a Internet.":::

Per correggere la configurazione:

1. Fare clic con il pulsante destro del mouse sull'icona del cloud nella mappa del dispositivo e selezionare **Esporta indirizzi IP**. Copiare gli intervalli pubblici privati e aggiungerli all'elenco di subnet. Per altre informazioni, vedere [configurare le subnet](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Genera un nuovo report di data mining per le connessioni Internet.

1. Nel report data mining selezionare :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: per attivare la modalità amministratore ed eliminare gli indirizzi IP dei dispositivi ICS.

### <a name="tweak-the-sensors-quality-of-service"></a>Modificare la qualità del servizio del sensore

Per salvare le risorse di rete, è possibile limitare la larghezza di banda dell'interfaccia utilizzata dal sensore per le procedure quotidiane.

Per limitare la larghezza di banda dell'interfaccia, usare lo strumento dell'interfaccia della riga di comando `cyberx-xsense-limit-interface` che deve essere eseguito con le autorizzazioni sudo. Lo strumento ottiene gli argomenti seguenti:

  - `* -i`: Interfaces (esempio: eth0).

  - `* -l`: limite (ad esempio: 30 kbit/1 Mbit). È possibile usare le unità di larghezza di banda seguenti: Kbps, Mbps, kbit, Mbit o bps.

  - `* -c`: Clear (per cancellare la limitazione della larghezza di banda dell'interfaccia).

Per ottimizzare la qualità del servizio:

1. Accedere all'interfaccia della riga di comando del sensore come Defender per l'utente Internet e immettere `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   ad esempio `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > Per un dispositivo fisico, usare l'interfaccia EM1.

1. Per cancellare la limitazione dell'interfaccia, immettere `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>Strumenti per la risoluzione dei problemi della console di gestione locale

### <a name="investigate-a-lack-of-expected-alerts"></a>Verificare la mancanza di avvisi previsti

Se nella finestra **avvisi** non viene visualizzato un avviso previsto, verificare quanto segue:

- Verificare che lo stesso avviso sia già presente nella finestra **avvisi** come risposta a un'altra istanza di sicurezza. Se sì, questo avviso non è ancora stato gestito, non viene visualizzato un nuovo avviso.

- Verificare che l'avviso non sia stato escluso usando le regole di **esclusione degli avvisi** nella console di gestione locale.  

### <a name="tweak-the-quality-of-service"></a>Ottimizzare la qualità del servizio

Per salvare le risorse di rete, è possibile limitare il numero di avvisi inviati a sistemi esterni (ad esempio messaggi di posta elettronica o SIEM) in un'unica operazione di sincronizzazione tra un'appliance e la console di gestione locale.

Il valore predefinito è 50. Ciò significa che in una sessione di comunicazione tra un'appliance e la console di gestione locale, non saranno presenti più di 50 avvisi per i sistemi esterni. 

Per limitare il numero di avvisi, utilizzare la `notifications.max_number_to_report` proprietà disponibile in `/var/cyberx/properties/management.properties` . Dopo la modifica di questa proprietà non è necessario riavviare il riavvio.

Per ottimizzare la qualità del servizio:

1. Accedere come difensore per l'utente. 

1. Verificare i valori predefiniti:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Vengono visualizzati i valori predefiniti seguenti:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Modificare le impostazioni predefinite:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Modificare le impostazioni delle righe seguenti:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Salvare le modifiche. Non occorre eseguire nessun riavvio.

## <a name="export-information-for-troubleshooting"></a>Esporta informazioni per la risoluzione dei problemi

Oltre agli strumenti per il monitoraggio e l'analisi della rete, è possibile inviare informazioni al team di supporto per un'analisi più approfondita. Quando si esportano i log, il sensore genererà automaticamente una password monouso (OTP), univoca per i log esportati, in un file di testo separato. 

Per esportare i log:

1. Nel riquadro sinistro selezionare impostazioni di **sistema**.

1. Selezionare **Esporta log**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Esportare un log nel supporto di sistema.":::

1. Nella casella **nome file** immettere il nome del file che si desidera utilizzare per l'esportazione dei log. L'impostazione predefinita è la data corrente.

1. Per definire i dati che si desidera esportare, selezionare le categorie di dati:  

    | Esporta categoria | Descrizione |
    |--|--|
    | **Log del sistema operativo** | Selezionare questa opzione per ottenere informazioni sullo stato del sistema operativo. |
    | **Log di installazione/aggiornamento** | Selezionare questa opzione per l'analisi dei parametri di configurazione dell'installazione e dell'aggiornamento. |
    | **Output di integrità del sistema** | Selezionare questa opzione per controllare le prestazioni del sistema. |
    | **Dissezione dei log** | Selezionare questa opzione per consentire l'ispezione avanzata della dissezione del protocollo. |
    | **Dump del kernel del sistema operativo** | Selezionare questa opzione per esportare il dump della memoria del kernel. Un dump della memoria del kernel contiene tutta la memoria usata dal kernel al momento del problema che si è verificato in questo kernel. Le dimensioni del file dump sono inferiori al dump di memoria completo. In genere, il file di dump è intorno a un terzo delle dimensioni della memoria fisica nel sistema. |
    | **Log di inoltri** | Selezionare questa opzione per ricercare le regole di invio. |
    | **Log SNMP** | Selezionare questa opzione per ricevere informazioni sul controllo di integrità SNMP. |
    | **Log applicazioni principali** | Selezionare questa opzione per esportare i dati sulla configurazione e sull'operazione principali dell'applicazione. |
    | **Comunicazione con i log CM** | Selezionare questa opzione se si verificano problemi continui o interruzioni della connessione con la console di gestione di. |
    | **Log applicazioni Web** | Selezionare questa opzione per ottenere informazioni su tutte le richieste inviate dall'interfaccia Web dell'applicazione. |
    | **Backup del sistema** | Selezionare questa opzione per esportare un backup di tutti i dati di sistema per l'analisi dello stato esatto del sistema. |
    | **Statistiche di dissezione** | Selezionare questa opzione per consentire l'ispezione avanzata delle statistiche del protocollo. |
    | **Log di database** | Selezionare questa opzione per esportare i log dal database di sistema. L'analisi dei registri di sistema consente di identificare i problemi di sistema. |
    | **Configuration** | Selezionare questa opzione per esportare informazioni su tutti i parametri configurabili per verificare che tutti gli elementi siano stati configurati correttamente. |

1. Per selezionare tutte le opzioni, selezionare **Seleziona tutto** accanto a **Scegli categorie**.

1. Selezionare **Esporta log**.

I log esportati vengono aggiunti all'elenco dei **log archiviati** . Inviare il OTP al team di supporto in un messaggio e un supporto separato dai log esportati. Il team di supporto potrà estrarre i log esportati solo usando l'OTP univoco usato per crittografare i log.

L'elenco dei log archiviati può contenere fino a cinque elementi. Se il numero di elementi nell'elenco supera tale numero, il primo elemento viene eliminato.

## <a name="see-also"></a>Vedere anche

- [Visualizzare gli avvisi](how-to-view-alerts.md)

- [Configurare il monitoraggio MIB SNMP](how-to-set-up-snmp-mib-monitoring.md)

- [Informazioni sugli eventi di disconnessione del sensore](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
