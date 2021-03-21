---
title: Attivare e configurare il sensore
description: Questo articolo descrive come eseguire l'accesso e attivare una console del sensore.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 98d59ec4b37238005e89051064a60fa6a4407394
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449833"
---
# <a name="activate-and-set-up-your-sensor"></a>Attivare e configurare il sensore

Questo articolo descrive come attivare un sensore ed eseguire l'installazione iniziale.

Gli utenti amministratori eseguono l'attivazione quando accedono per la prima volta e quando è necessaria la gestione dell'attivazione. Il programma di installazione garantisce che il sensore sia configurato per il rilevamento e l'avviso ottimali.

Gli analisti della sicurezza e gli utenti di sola lettura non possono attivare un sensore o generare una nuova password.

## <a name="sign-in-and-activation-for-administrator-users"></a>Accesso e attivazione per gli utenti amministratore

Gli amministratori che accedono per la prima volta devono verificare di avere accesso ai file di attivazione e ripristino della password scaricati durante l'onboarding del sensore. In caso contrario, sono necessarie le autorizzazioni di amministratore della sicurezza di Azure, collaboratore sottoscrizione o proprietario della sottoscrizione per generare questi file nel portale di Azure Defender per l'it.

### <a name="first-time-sign-in-and-activation-checklist"></a>Elenco di controllo per il primo accesso e l'attivazione

Prima di accedere alla console del sensore, gli utenti amministratori devono avere accesso a:

- Indirizzo IP del sensore definito durante l'installazione.

- Credenziali di accesso utente per il sensore. Se è stato scaricato un file ISO per il sensore, usare le credenziali predefinite ricevute durante l'installazione. Si consiglia di creare un nuovo utente *amministratore* dopo l'attivazione.

- Password iniziale. Se è stato acquistato un sensore preconfigurato dalla freccia, è necessario generare una password al momento dell'accesso per la prima volta.

- File di attivazione associato a questo sensore. Il file è stato generato e scaricato durante l'onboarding dei sensori nel portale Defender for Internet.

- Un certificato firmato da un'autorità di certificazione SSL/TLS richiesto dall'azienda.

### <a name="about-activation-files"></a>Informazioni sui file di attivazione

Il sensore è stato caricato in Azure Defender per gli utenti in una modalità di gestione specifica:

| Tipo di modalità | Descrizione |
|--|--|
| **Modalità di connessione cloud** | Le informazioni rilevate dal sensore vengono visualizzate nella console del sensore. Le informazioni sugli avvisi vengono fornite anche tramite l'hub Internet e possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel. |
| **Modalità connessa localmente** | Le informazioni rilevate dal sensore vengono visualizzate nella console del sensore. Le informazioni di rilevamento vengono condivise anche con la console di gestione locale, se il sensore è connesso. |

Un file di attivazione connesso localmente o connesso al cloud è stato generato e scaricato per questo sensore durante l'onboarding. Il file di attivazione contiene istruzioni per la modalità di gestione del sensore. *È necessario caricare un file di attivazione univoco in ogni sensore distribuito.*  La prima volta che si accede, è necessario caricare il file di attivazione pertinente per questo sensore.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender per il portale di Internet delle cose, onboarding del sensore.":::

### <a name="about-certificates"></a>Informazioni sui certificati

Dopo l'installazione del sensore, viene generato e utilizzato un certificato autofirmato locale per accedere alla console del sensore. Quando un amministratore accede alla console per la prima volta, all'utente viene richiesto di caricare un certificato SSL/TLS.

Sono disponibili due livelli di sicurezza:

- Soddisfare i requisiti specifici di certificato e crittografia richiesti dall'organizzazione, caricando il certificato firmato da un'autorità di certificazione.
- Consente la convalida tra la console di gestione e i sensori connessi. La convalida viene valutata in base a un elenco di revoche di certificati e alla data di scadenza del certificato. *Se la convalida ha esito negativo, la comunicazione tra la console di gestione e il sensore viene interrotta e viene visualizzato un errore di convalida nella console.* Questa opzione è abilitata per impostazione predefinita dopo l'installazione.  

La console supporta i tipi di certificato seguenti:

- Infrastruttura a chiave privata ed Enterprise (PKI privata)

- Infrastruttura a chiave pubblica (PKI pubblico)

- Generato localmente nel dispositivo (autofirmato locale) 

  > [!IMPORTANT]
  > Si consiglia di non usare il certificato autofirmato predefinito. Il certificato non è sicuro e deve essere usato solo per gli ambienti di test. Il proprietario del certificato non può essere convalidato e non è possibile mantenere la sicurezza del sistema. Non usare mai questa opzione per le reti di produzione.

### <a name="sign-in-and-activate-the-sensor"></a>Accedere e attivare il sensore

Per accedere e attivare:

1. Passare alla console del sensore dal browser usando l'IP definito durante l'installazione. Verrà visualizzata la finestra di dialogo di accesso.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender per il sensore Internet.":::

1. Immettere le credenziali definite durante l'installazione del sensore oppure selezionare l'opzione per il **ripristino della password** . Se è stato acquistato un sensore preconfigurato dalla freccia, generare prima una password. Per altre informazioni sul ripristino delle password, vedere [esaminare l'errore della password all'accesso iniziale](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Dopo aver eseguito l'accesso, viene visualizzata la finestra di dialogo di **attivazione** . Selezionare **carica** e passare al file di attivazione scaricato durante l'onboarding del sensore.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Selezionare carica e passare al file di attivazione.":::

1. Selezionare il collegamento **configurazione di rete del sensore** se si vuole modificare la configurazione di rete del sensore prima dell'attivazione. Vedere [aggiornare la configurazione di rete del sensore prima dell'attivazione](#update-sensor-network-configuration-before-activation).

1. Accettare i termini e le condizioni.

1. Selezionare **Attiva**. Verrà visualizzata la finestra di dialogo certificato SSL/TLS.

1. Definire un nome di certificato.
1. Caricare i file CRT e Key.
1. Immettere una passphrase e caricare un file PEM, se necessario.
1. Selezionare **Avanti**. Verrà visualizzata la schermata convalida. Per impostazione predefinita, la convalida tra la console di gestione e i sensori connessi è abilitata.
1. Disattivare l'opzione **Abilita convalida a livello di sistema** per disabilitare la convalida. Si consiglia di abilitare la convalida.
1. Selezionare **Salva**.  

Potrebbe essere necessario aggiornare la schermata dopo aver caricato il certificato firmato da un'autorità di certificazione.

Per informazioni sul caricamento di un nuovo certificato, sui parametri del certificato supportati e sull'uso dei comandi del certificato CLI, vedere [gestire i singoli sensori](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Aggiornare la configurazione di rete del sensore prima dell'attivazione  

I parametri di configurazione di rete del sensore sono stati definiti durante l'installazione del software o quando è stato acquistato un sensore preconfigurato. Sono stati definiti i parametri seguenti:

- Indirizzo IP
- DNS
- Gateway predefinito
- Subnet mask
- Nome host

Potrebbe essere necessario aggiornare queste informazioni prima di attivare il sensore. Ad esempio, potrebbe essere necessario modificare i parametri preconfigurati definiti da Arrow. Prima di attivare il sensore, è anche possibile definire le impostazioni del proxy.

Per aggiornare i parametri di configurazione della rete del sensore:

1. Selezionare il collegamento **configurazione di rete del sensore** dalla finestra di dialogo di **attivazione** .

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Configurazione di rete del sensore.":::

2. Vengono visualizzati i parametri definiti durante l'installazione. È disponibile anche l'opzione per definire il proxy. Aggiornare le impostazioni come richiesto e selezionare **Salva**.

### <a name="subsequent-sign-ins"></a>Accessi successivi

Dopo l'attivazione per la prima volta, la console di Azure Defender per il sensore Internet viene aperta dopo l'accesso senza richiedere un file di attivazione. Sono necessarie solo le credenziali di accesso.

Dopo l'accesso, viene visualizzata la console di Azure Defender for Internet.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender per la console.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Installazione e apprendimento iniziali (per gli amministratori)

Dopo il primo accesso, il sensore di Azure Defender per l'it inizia a monitorare automaticamente la rete. I dispositivi di rete verranno visualizzati nelle sezioni Mappa del dispositivo e inventario dei dispositivi. Azure Defender per le cose inizia a rilevare e ricevere avvisi per tutti gli eventi di sicurezza e operativi che si verificano nella rete. È quindi possibile creare report e query in base alle informazioni rilevate.

Inizialmente questa attività viene eseguita nella modalità di apprendimento, che indica al sensore di apprendere le normali attività della rete. Ad esempio, il sensore apprende i dispositivi individuati nella rete, i protocolli rilevati nella rete e i trasferimenti di file che si verificano tra dispositivi specifici. Questa attività diventa l'attività Baseline della rete.

### <a name="review-and-update-basic-system-settings"></a>Rivedere e aggiornare le impostazioni di sistema di base

Esaminare le impostazioni di sistema del sensore per verificare che il sensore sia configurato per il rilevamento e l'avviso ottimali.

Definire le impostazioni di sistema del sensore. Ad esempio:

- Definire ICS (o Internet) e subnet separate.

- Definire gli alias di porta per i protocolli specifici del sito.

- Definire le VLAN e i nomi in uso.

- Se DHCP è in uso, definire intervalli DHCP legittimi.

- Definire l'integrazione con Active Directory e il server di posta elettronica in base alle esigenze.

### <a name="disable-learning-mode"></a>Disabilitare la modalità di apprendimento

Dopo aver modificato le impostazioni di sistema, è possibile lasciare che il sensore Azure Defender for Internet all venga eseguito in modalità di apprendimento fino a quando non si ritiene che il rilevamento del sistema rispecchi accuratamente l'attività di rete.

La modalità di apprendimento deve essere eseguita da circa 2 a 6 settimane, a seconda delle dimensioni e della complessità della rete. Dopo aver disabilitato la modalità di apprendimento, qualsiasi attività che differisce dall'attività di base attiverà un avviso.

Per disabilitare la modalità di apprendimento:

- Selezionare **impostazioni di sistema** e disattivare l'opzione di **apprendimento** .

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Accesso per la prima volta per gli analisti della sicurezza e gli utenti di sola lettura

Prima di eseguire l'accesso, verificare di avere:

- Indirizzo IP del sensore.
- Credenziali di accesso fornite dall'amministratore.

## <a name="console-tools-overview"></a>Strumenti della console: Panoramica

È possibile accedere agli strumenti della console dal menu laterale.

**Spostamento** 

| Finestra | Icona | Descrizione |
| -----------|--|--|
| Dashboard | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Visualizzare uno snapshot intuitivo dello stato della sicurezza della rete. |
| Mappa dei dispositivi | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Visualizzare i dispositivi di rete, le connessioni del dispositivo e le proprietà del dispositivo in una mappa. Per la visualizzazione della rete sono disponibili diverse opzioni di zoom, evidenziazione e filtro. |
| Inventario dei dispositivi | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | L'inventario del dispositivo visualizza un elenco di attributi del dispositivo rilevati dal sensore. Sono disponibili opzioni per: <br /> -Ordinare o filtrare le informazioni in base ai campi della tabella e visualizzare le informazioni filtrate visualizzate. <br /> -Esportare le informazioni in un file CSV. <br /> -Importa i dettagli del registro di sistema di Windows.|
| Avvisi | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Visualizza gli avvisi quando si verificano violazioni dei criteri, si verificano deviazioni dal comportamento della linea di base o viene rilevato qualsiasi tipo di attività sospetta nella rete. |
| Report | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Visualizzazione di report basati sulle query di data mining. |

**Analisi**

| Finestra| Icona | Descrizione |
|---|---|---|
| Sequenza temporale eventi | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Visualizzare una sequenza temporale con informazioni su avvisi, eventi di rete (informazioni) e operazioni dell'utente, ad esempio accessi utente e eliminazioni utente.|

**Spostamento**

| Finestra | Icona | Descrizione |
|---|---|---|
| Data mining | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Genera informazioni complete e granulari sui dispositivi della rete a vari livelli. |
| Analisi | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Visualizzare tendenze e statistiche in un'ampia gamma di widget. |
| Valutazione dei rischi | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Visualizzare la finestra **vulnerabilità** . |

**Admin**

| Finestra | Icona | Descrizione |
|---|---|---|
| Utenti | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Definire utenti e ruoli con diversi livelli di accesso. |
| Inoltro | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Invia le informazioni sugli avvisi ai partner e alle origini interne (ad esempio, Azure Sentinel), integrando con Defender per l'it, per gli indirizzi di posta elettronica, i server webhook e altro ancora. <br /> Per informazioni dettagliate, vedere [trasmettere informazioni sugli avvisi](how-to-forward-alert-information-to-partners.md) . |
| Impostazioni di sistema | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Configurare le impostazioni di sistema. Ad esempio, definire le impostazioni DHCP, fornire i dettagli del server di posta o creare alias di porta. |
| Importare impostazioni | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Visualizzare la finestra **Importa impostazioni** . È possibile eseguire modifiche manuali nelle informazioni di un dispositivo.<br /> Per informazioni dettagliate, vedere [importare informazioni sul dispositivo](how-to-import-device-information.md) . |

**Supporto**

| Finestra| Icona | Descrizione |
|----|---|---|
| Supporto | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Per assistenza, contattare [supporto tecnico Microsoft](https://support.microsoft.com/) . |

## <a name="see-also"></a>Vedi anche

[Eseguire l'onboarding di un sensore](getting-started.md#onboard-a-sensor)

[Gestire i file di attivazione dei sensori](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Controllare quale traffico viene monitorato](how-to-control-what-traffic-is-monitored.md)
