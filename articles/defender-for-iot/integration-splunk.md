---
title: Informazioni sull'integrazione di Splunk
titleSuffix: Azure Defender for IoT
description: Per risolvere la mancanza di visibilità per la sicurezza e la resilienza delle reti OT, Defender for Internet IT ha sviluppato il Defender per Internet delle cose, IIoT e l'applicazione di monitoraggio delle minacce ICS per Splunk, un'integrazione nativa tra Defender for Internet e Splunk che consente un approccio unificato all'IT e alla sicurezza.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557987"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Defender per l'applicazione di monitoraggio delle minacce e ICS per Splunk

Defender for Internet delle cose mitiga i rischi IIoT, ICS e SCADA grazie ai motori di apprendimento automatico compatibili con ICS, che offrono informazioni immediate sui dispositivi ICS, sulle vulnerabilità e sulle minacce in meno di un'ora di immagine e senza basarsi su agenti, regole o firme, competenze specializzate o conoscenze precedenti dell'ambiente.

Per risolvere la mancanza di visibilità per la sicurezza e la resilienza delle reti OT, Defender for Internet IT ha sviluppato il Defender per Internet delle cose, IIoT e l'applicazione di monitoraggio delle minacce ICS per Splunk, un'integrazione nativa tra Defender for Internet e Splunk che consente un approccio unificato all'IT e alla sicurezza.

> [!Note]
> I riferimenti a CyberX fanno riferimento ad Azure Defender per l'it.

## <a name="about-the-splunk-application"></a>Informazioni sull'applicazione Splunk

L'applicazione fornisce analisti SOC con visibilità multidimensionale nei protocolli OT specializzati e nei dispositivi IIoT distribuiti in ambienti industriali, insieme a analisi comportamentale compatibili con ICS, per rilevare rapidamente comportamenti sospetti o anomali. L'applicazione Abilita anche l'IT e la risposta agli eventi imprevisti dall'interno di un SOC aziendale. Si tratta di un'evoluzione importante in base alla convergenza continua e all'OT per supportare nuove iniziative IIoT, ad esempio smart machine e intelligence in tempo reale.

L'applicazione Splunk può essere installata localmente o eseguita in un cloud. L'integrazione con Defender for Internet è supportata da entrambe le distribuzioni.

## <a name="about-the-integration"></a>Informazioni sull'integrazione

L'integrazione di Defender for Splunk tramite l'applicazione nativa consente agli utenti di:

- Riduci il tempo necessario per le organizzazioni di infrastruttura industriali e critiche per rilevare, analizzare e agire sulle minacce informatiche.

- Ottenere informazioni in tempo reale sui rischi di OT.

- Correlare Defender per gli avvisi Internet con i repository Splunk Enterprise Security Threat Intelligence.

- Monitorare e rispondere da un singolo riquadro di vetro.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Pagina principale dello strumento Splunk.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="La pagina degli avvisi in Splunk.":::

L'applicazione consente agli amministratori di Splunk di analizzare gli avvisi di OT che Defender per l'invio di informazioni su Internet e di monitorare l'intera distribuzione di sicurezza OT, inclusi i dettagli, ad esempio:

- Quale dei cinque motori di analisi ha rilevato l'avviso.

- Il protocollo che ha generato l'avviso.

- Quale Defender per il sensore di Internet delle cose ha generato l'avviso.

- Livello di gravità dell'avviso.

- Origine e destinazione della comunicazione.

## <a name="requirements"></a>Requisiti

### <a name="version-requirements"></a>Requisiti della versione

Le versioni seguenti sono i requisiti.

- Defender per la versione 2,4 e successive.

- Splunkbase versione 11 e successive.

- Splunk Enterprise versione 7,2 e successive.
  
## <a name="download-the-application"></a>Scaricare l'applicazione

Scaricare l' *applicazione CYBERX ICS Threat Monitoring for Splunk* da [splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Requisiti delle autorizzazioni di Splunk

È richiesta l'autorizzazione Splunk seguente:

- Qualsiasi utente con autorizzazioni per il ruolo utente *amministratore* .

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Inviare il Defender per gli avvisi relativi a Splunk

Gli avvisi di Defender for Internet Information forniscono informazioni su una vasta gamma di eventi di sicurezza, tra cui:

- Deviazioni dall'attività di rete di base acquisita.

- Rilevamenti malware.

- Rilevamenti basati su modifiche operative sospette.

- Anomalie di rete.

- Deviazioni del protocollo dalle specifiche del protocollo.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="Schermata dei rilevamenti.":::

È possibile configurare Defender per l'invio di avvisi al server Splunk, in cui le informazioni sugli avvisi vengono visualizzate nel dashboard di Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Visualizzare tutti gli avvisi e i relativi dettagli.":::

Le informazioni di avviso seguenti vengono inviate al server Splunk.

- Data e ora dell'avviso.

- Il Defender per il motore delle cose che ha rilevato l'evento: violazione del protocollo, violazione dei criteri, malware, anomalia o motore operativo.

- Titolo dell'avviso.

- Messaggio di avviso.

- Gravità dell'avviso: Warning, minor, Major o Critical.

- Nome del dispositivo di origine.

- Indirizzo IP del dispositivo di origine.

- Nome del dispositivo di destinazione.

- Indirizzo IP del dispositivo di destinazione.

- Il Defender per l'indirizzo IP della piattaforma Internet (host).

- Nome del dispositivo Defender per l'appliance della piattaforma Internet (tipo di origine).

Di seguito è riportato l'output di esempio:

| Tempo | Event |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Avviso della piattaforma Defender for** Internet: un dispositivo è stato arrestato da un comando del PLC<br /><br />**Tipo**: violazione operativa <br /><br />**Gravità**: principale <br /><br />**Nome origine**: my_device1 <br /><br />**IP di origine**: 192.168.110.131 <br /><br />**Nome destinazione**: my_device2<br /><br /> **IP di destinazione**: 10.140.33.238 <br /><br />**Messaggio**: un dispositivo di rete è stato interrotto utilizzando un comando Arresta PLC. Questo dispositivo non funzionerà fino a quando non viene inviato un comando di avvio. 192.168.110.131 è stato arrestato da 10.140.33.238 (un dispositivo Siemens S7), usando un comando di arresto del PLC.<br /><br />**Host**: 192.168.90.43 <br /><br />**SourceType**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Definire le regole di invio degli avvisi

Usare Defender per *le regole di inoltri* per l'invio di informazioni sugli avvisi ai server Splunk.

Sono disponibili opzioni per personalizzare le regole di avviso in base a:

- Protocolli specifici rilevati.

- Gravità dell'evento.

- Defender per il motore delle cose che rileva gli eventi.

Per creare una regola di invio:

1. Dal sensore o dal riquadro sinistro della console di gestione locale selezionare **inoltri.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Selezionare il pulsante blu crea avviso di invio.":::

1. Selezionare **Crea regole di inoltri**. Nella finestra **Crea regola di invio** definire i parametri della regola.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Creare le regole per la regola di invio.":::

    | Parametro | Descrizione |
    |--|--|
    | **Nome** | Nome della regola di inoltri. |
    | **Seleziona gravità** | Evento imprevisto minimo del livello di sicurezza da trasmettere. Se, ad esempio, è selezionata l'opzione minore, verranno trasmessi gli avvisi secondari e gli eventuali avvisi al di sopra di tale livello di gravità. |
    | **Protocolli** | Per impostazione predefinita, vengono selezionati tutti i protocolli. Per selezionare un protocollo specifico, selezionare **specifico** e selezionare il protocollo per cui viene applicata la regola. |
    | **Motori** | Per impostazione predefinita, sono interessati tutti i motori di sicurezza. Per selezionare un motore di sicurezza specifico a cui applicare la regola, selezionare **specifico** e selezionare il motore. |
    | **Notifiche di sistema** | Stato di avanzamento del sensore online/offline. Questa opzione è disponibile solo se è stato effettuato l'accesso a gestione centrale. |                                            |

1. Per indicare a Defender per l'invio di informazioni sugli asset a Splunk, selezionare **azione** e quindi selezionare **Invia a server Splunk**.

1. Immettere i parametri di Splunk seguenti.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Parametri Splunk da immettere in questa schermata.":::

    | Parametro | Description |
    |--|--|
    | **Host** | Indirizzo del server Splunk |
    | **Porta** | 8089 |
    | **Nome utente** | Nome utente del server Splunk |
    | **Password** | Password del server Splunk |

1. Selezionare **Invia**

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [inviare le informazioni sugli avvisi](how-to-forward-alert-information-to-partners.md).
