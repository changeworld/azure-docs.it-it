---
title: Informazioni sugli avvisi in diretta
description: È possibile inviare informazioni sugli avvisi ai sistemi partner usando le regole di invio.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0b71f7ca3f812de1514612f8b0dd5915f3f81bc4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841144"
---
# <a name="forward-alert-information"></a>Informazioni sugli avvisi in diretta

È possibile inviare informazioni sugli avvisi ai partner che si integrano con Azure Defender per l'it, ai server syslog, agli indirizzi di posta elettronica e altro ancora. L'uso delle regole di invio consente di fornire rapidamente informazioni sugli avvisi agli stakeholder di sicurezza.  

Syslog e altre azioni di invio predefinite vengono fornite con il sistema. È possibile che vengano rese disponibili altre azioni di invio quando si integrano con i fornitori partner, ad esempio Microsoft Azure Sentinel, ServiceNow o Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Informazioni sugli avvisi.":::

Defender per gli amministratori di Internet delle cose hanno le autorizzazioni per usare le regole di invio.

## <a name="about-forwarded-alert-information"></a>Informazioni sulle informazioni sugli avvisi con inoltri

Gli avvisi forniscono informazioni su una vasta gamma di eventi operativi e di sicurezza. Ad esempio:

  - Data e ora dell'avviso

  - Motore che ha rilevato l'evento

  - Titolo dell'avviso e messaggio descrittivo

  - Gravità dell'avviso

  - Nome e indirizzo IP di origine e destinazione

  - Rilevato traffico sospetto

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Analisi degli indirizzi rilevata.":::

Quando vengono create regole di invio, le informazioni rilevanti vengono inviate ai sistemi partner.

## <a name="create-forwarding-rules"></a>Creare regole di inoltri

Per creare una nuova regola di invio:

1. Selezionare **Avanti** dal menu laterale.

   :: image type = "Content" Source = "media/how-to-work-with-Alerts-Sensor/create-forwarding-rule-screen.png" alt-text = "creare un'icona della regola di invio.":::

2. Selezionare **Crea regola di invio**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Creare una nuova regola di invio.":::

3. Immettere il nome della regola di invio.

### <a name="forwarding-rule-criteria"></a>Criteri della regola di invio 

Definire i criteri in base ai quali attivare una regola di invio. L'utilizzo dei criteri della regola di invio consente di individuare e gestire il volume delle informazioni inviate dal sensore ai sistemi esterni. Sono disponibili le opzioni seguenti:

**Protocolli**: attivare la regola di invio solo se il traffico rilevato era in esecuzione su protocolli specifici. Selezionare i protocolli richiesti dall'elenco a discesa oppure sceglierli tutti.

**Motori**: selezionare i motori richiesti oppure sceglierli tutti. Verranno inviati gli avvisi dei motori selezionati.

**Livelli di gravità**: si tratta dell'evento imprevisto minimo da trasmettere, in termini di livello di gravità. Se ad esempio si seleziona **secondario**, gli avvisi secondari e gli eventuali avvisi al di sopra di questo livello di gravità verranno trasmessi. I livelli sono predefiniti.

### <a name="forwarding-rule-actions"></a>Azioni regola di invio

Le azioni della regola di invio indicano al sensore di trasmettere le informazioni sugli avvisi ai fornitori o ai server partner. È possibile creare più azioni per ogni regola di invio.

Oltre alle azioni di invio fornite con il sistema, è possibile che altre azioni diventino disponibili quando si integrano con i fornitori partner. 

#### <a name="email-address-action"></a>Azione indirizzo di posta elettronica

Inviare un messaggio di posta elettronica che include le informazioni sull'avviso. È possibile immettere un indirizzo di posta elettronica per ogni regola.

Per definire la posta elettronica per la regola di invio:

1. Immettere un unico indirizzo di posta elettronica. Se è necessario inviare più di un messaggio di posta elettronica, creare un'altra azione.

2. Immettere il fuso orario per il timestamp del rilevamento degli avvisi in SIEM.

3. Selezionare **Submit** (Invia).

#### <a name="syslog-server-actions"></a>Azioni del server syslog

Sono supportati i formati seguenti:

- SMS

- Messaggi CEF

- Messaggi LEEF

- Messaggi oggetto

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Creare azioni della regola di invio.":::

Immettere i parametri seguenti:

- Nome host syslog e porta.

- Protocollo TCP e UDP.

- Fuso orario per il timestamp del rilevamento degli avvisi in SIEM.

- File del certificato di crittografia TLS e file di chiave per i server CEF (facoltativo).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Configurare la crittografia per la regola di invio.":::

| Campi di output del messaggio di testo syslog | Descrizione |
|--|--|
| Data e ora | Data e ora in cui il computer del server syslog ha ricevuto le informazioni. |
| Priorità | Utente. Alert |
| Nome host | Indirizzo IP del sensore |
| Protocollo | TCP o UDP |
| Messaggio | Sensore: il nome del sensore.<br /> Alert: titolo dell'avviso.<br /> Tipo: tipo di avviso. Può essere **violazione del protocollo**, **violazione dei criteri**, **malware**, **anomalie** o **operativo**.<br /> Gravità: gravità dell'avviso. Può essere **warning**, **minor**, **Major** o **Critical**.<br /> Source: il nome del dispositivo di origine.<br /> IP di origine: indirizzo IP del dispositivo di origine.<br /> Destinazione: il nome del dispositivo di destinazione.<br /> IP di destinazione: indirizzo IP del dispositivo di destinazione.<br /> Message: messaggio dell'avviso.<br /> Gruppo avvisi: il gruppo di avvisi associato all'avviso. |


| Output dell'oggetto syslog | Descrizione |
|--|--|
| Data e ora |   Data e ora in cui il computer del server syslog ha ricevuto le informazioni. |  
| Priorità |    Utente. Alert | 
| Nome host |    IP del sensore | 
| Messaggio | Nome del sensore: il nome dell'appliance. <br /> Ora avviso: l'ora in cui è stato rilevato l'avviso: può variare dall'ora del computer server syslog e dipende dalla configurazione del fuso orario della regola di invio. <br /> Titolo dell'avviso: titolo dell'avviso. <br /> Messaggio di avviso: messaggio dell'avviso. <br /> Gravità dell'avviso: gravità dell'avviso: **avviso**, **secondario**, **principale** o **critico**. <br /> Tipo di avviso **: violazione del protocollo**, violazione dei **criteri**, **malware**, **anomalie** o **operativo**. <br /> Protocollo: il protocollo dell'avviso.  <br /> **Source_MAC**: indirizzo IP, nome, fornitore o sistema operativo del dispositivo di origine. <br /> Destination_MAC: indirizzo IP, nome, fornitore o sistema operativo della destinazione. Se i dati sono mancanti, il valore sarà **N/A**. <br /> alert_group: il gruppo di avvisi associato all'avviso. |


| Formato di output CEF syslog | Descrizione |
|--|--|
| Data e ora | Data e ora in cui il computer del server syslog ha ricevuto le informazioni. |
| Priorità | Utente. Alert | 
| Nome host | Indirizzo IP del sensore |
| Messaggio | CEF: 0 <br />Azure Defender per IoT <br />Nome del sensore: il nome dell'appliance del sensore. <br />Versione del sensore <br />Titolo dell'avviso: titolo dell'avviso. <br />Msg: messaggio dell'avviso. <br />protocollo: il protocollo dell'avviso. <br />gravità: **avviso**, **secondario**, **principale** o **critico**. <br />Tipo: **violazione del protocollo**, **violazione dei criteri**, **malware**, **anomalie** o **operativo**. <br /> Start: ora in cui è stato rilevato l'avviso. <br />Potrebbe variare dall'ora del computer server syslog e dipende dalla configurazione del fuso orario della regola di invio. <br />src_ip: indirizzo IP del dispositivo di origine.  <br />dst_ip: indirizzo IP del dispositivo di destinazione.<br />Cat: gruppo di avvisi associato all'avviso.  |

| Formato di output LEEF syslog | Descrizione |
|--|--|
| Data e ora |   Data e ora in cui il computer del server syslog ha ricevuto le informazioni. |  
| Priorità |    Utente. Alert | 
| Nome host |    IP del sensore |
| Messaggio | Nome del sensore: il nome dell'appliance Azure Defender per le cose. <br />LEEF: 1.0 <br />Azure Defender per IoT <br />Sensore  <br />Versione del sensore <br />Avviso di Azure Defender per le cose <br />title: titolo dell'avviso. <br />Msg: messaggio dell'avviso. <br />protocollo: il protocollo dell'avviso.<br />gravità: **avviso**, **secondario**, **principale** o **critico**. <br />Tipo: tipo di avviso: violazione del **protocollo**, **violazione dei criteri**, **malware**, **anomalia** o **operativo**. <br />Start: ora dell'avviso.Si noti che potrebbe essere diverso dall'ora del computer del server syslog. (Dipende dalla configurazione del fuso orario). <br />src_ip: indirizzo IP del dispositivo di origine.<br />dst_ip: indirizzo IP del dispositivo di destinazione. <br />Cat: gruppo di avvisi associato all'avviso. |

Dopo aver immesso tutte le informazioni, selezionare **Invia**.

#### <a name="netwitness-action"></a>Azione NetWitness

Inviare informazioni sugli avvisi a un server NetWitness.

Per definire i parametri di NetWitness per l'invio:

1. Immettere le informazioni sulla **porta** e il **nome host** NetWitness.

2. Immettere il fuso orario per il timestamp del rilevamento degli avvisi in SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Aggiungere un fuso orario alla regola di invio.":::

3. Selezionare **Submit** (Invia).

#### <a name="integrated-vendor-actions"></a>Azioni del fornitore integrate

Il sistema potrebbe essere stato integrato con una sicurezza, una gestione dei dispositivi o un altro fornitore del settore. Queste integrazioni consentono di:

  - Inviare informazioni sull'avviso.

  - Inviare le informazioni di inventario del dispositivo.

  - Comunicare con i firewall lato fornitore.

Gli integrazioni consentono di colmare le soluzioni di sicurezza precedentemente silo, migliorare la visibilità dei dispositivi e accelerare la risposta a livello di sistema per attenuare più rapidamente i rischi.

Usare la sezione azioni per immettere le credenziali e altre informazioni necessarie per comunicare con i fornitori integrati.

Per informazioni dettagliate sulla configurazione delle regole di invio per le integrazioni, vedere gli articoli relativi all'integrazione dei partner.

### <a name="test-forwarding-rules"></a>Regole di invio test

Testare la connessione tra il sensore e il server partner definito nelle regole di invio:

1. Selezionare la regola dalla finestra di dialogo **regola di inoltri** .

2. Selezionare la casella **altro** .

3. Selezionare **Invia messaggio di prova**.

4. Passare al sistema partner per verificare che le informazioni inviate dal sensore siano state ricevute.

### <a name="edit-and-delete-forwarding-rules"></a>Modificare ed eliminare regole di invio 

Per modificare una regola di invio:

- Nella schermata della **regola di inoltri** selezionare **modifica** nel menu a discesa **altro** . Apportare le modifiche desiderate e selezionare **Invia**.

Per rimuovere una regola di invio:

- Nella schermata della **regola di inoltri** selezionare **Rimuovi** nel menu a discesa **altro** . Nella finestra di dialogo di **avviso** fare clic su **OK**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Regole di invio e regole di esclusione degli avvisi

L'amministratore potrebbe avere definito regole di esclusione degli avvisi. Queste regole consentono agli amministratori di ottenere un controllo più granulare sull'attivazione degli avvisi indicando al sensore di ignorare gli eventi di avviso in base a diversi parametri. Questi parametri possono includere indirizzi di dispositivo, nomi di avvisi o sensori specifici.

Ciò significa che le regole di invio definite potrebbero essere ignorate in base alle regole di esclusione create dall'amministratore. Le regole di esclusione sono definite nella console di gestione locale.

## <a name="see-also"></a>Vedere anche

[Accelerare i flussi di lavoro degli avvisi](how-to-accelerate-alert-incident-response.md)
