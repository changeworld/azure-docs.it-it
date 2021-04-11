---
title: Informazioni sugli avvisi del sensore
description: Usare gli avvisi per migliorare la sicurezza e il funzionamento della rete.
ms.date: 3/29/2021
ms.topic: how-to
ms.openlocfilehash: a34871342e9a868ba593ae894fdd91b5e99c5a44
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729423"
---
# <a name="about-sensor-alerts"></a>Informazioni sugli avvisi del sensore

Gli avvisi consentono di migliorare la sicurezza e il funzionamento della rete. Gli avvisi forniscono informazioni su:

- Deviazioni dall'attività di rete autorizzata

- Protocollo e anomalie operative

- Traffico malware sospetto

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Rileva analisi indirizzi.":::

Le opzioni di gestione degli avvisi consentono agli utenti di:

- Indica ai sensori di apprendere le attività rilevate come traffico autorizzato.

- Confermare la revisione dell'avviso.

- Indicare ai sensori di disattivare gli eventi rilevati con dispositivi identici e traffico analogo.

Sono disponibili strumenti aggiuntivi che consentono di migliorare e velocizzare l'analisi degli avvisi. Ad esempio:

  - Aggiungere commenti istruttivi per i revisori degli avvisi.

  - Creare gruppi di avvisi per la visualizzazione in soluzioni SOC. 

  - Ricerca di avvisi specifici; esaminare i file PCAP correlati; visualizzare i dispositivi rilevati e altri dispositivi connessi nella mappa del dispositivo o inviare i dettagli degli avvisi ai sistemi partner.

  - Inviare avvisi ai fornitori di partner: sistemi SIEM, sistemi MSSP e altro ancora.

## <a name="alerts-and-engines"></a>Avvisi e motori

Gli avvisi vengono attivati quando i motori dei sensori rilevano le modifiche nel traffico di rete e il comportamento che richiedono attenzione. Questo articolo descrive il tipo di avvisi attivati da ogni motore.

| Tipo di avviso | Descrizione |
|-|-|
| Avvisi di violazione dei criteri | Attivato quando il motore di violazione dei criteri rileva una deviazione dal traffico appreso in precedenza. Ad esempio: <br /> -Viene rilevato un nuovo dispositivo.  <br /> -Viene rilevata una nuova configurazione in un dispositivo. <br /> -Un dispositivo non definito come dispositivo di programmazione comporta una modifica della programmazione. <br /> -È stata modificata una versione del firmware. |
| Avvisi di violazione del protocollo | Attivato quando il motore di violazione del protocollo rileva le strutture di pacchetti o i valori dei campi che non sono conformi alla specifica del protocollo. | 
| Avvisi operativi | Attivato quando il motore operativo rileva eventi imprevisti operativi di rete o malfunzionamenti del dispositivo. Ad esempio, un dispositivo di rete è stato interrotto tramite un comando Stop PLC oppure un'interfaccia su un sensore ha interrotto il monitoraggio del traffico. |
| Avvisi malware | Attivato quando il motore malware rileva un'attività di rete dannosa. Ad esempio, il motore rileva un attacco noto come Conficker. |
| Avvisi di anomalie | Attivato quando il motore delle anomalie rileva una deviazione. Ad esempio, un dispositivo esegue analisi di rete, ma non è definito come dispositivo di analisi. |

Sono disponibili strumenti per abilitare e disabilitare i motori di sensori. Gli avvisi non vengono attivati dai motori che sono disabilitati. Vedere [controllare il traffico monitorato](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Segnalazione di avvisi e sensori

Quando si generano report di data mining, valutazione dei rischi e vettori di attacco, è possibile calcolare le attività riflesse negli avvisi. Quando si gestiscono questi eventi, il sensore aggiorna i report di conseguenza.

Ad esempio:

  - La connettività non autorizzata tra un dispositivo in una subnet definita e i dispositivi che si trovano all'esterno della subnet (Public) verranno visualizzati nel report *attività Internet* di data mining e nella sezione valutazione dei rischi per le *connessioni Internet* . Una volta che questi dispositivi sono autorizzati (appresi), vengono calcolati nella generazione di questi report.

  - Gli eventi malware rilevati sui dispositivi di rete vengono segnalati nei report di valutazione dei rischi. Quando gli avvisi sugli eventi malware vengono *disabilitati*, i dispositivi interessati non verranno calcolati nel report di valutazione dei rischi.

## <a name="next-steps"></a>Passaggi successivi

[Apprendimento e modalità](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes) 
 di apprendimento it intelligenti [Visualizza le informazioni fornite negli avvisi](how-to-view-information-provided-in-alerts.md) 
 [Gestire l'evento](how-to-manage-the-alert-event.md) 
 di avviso [Accelerare i flussi di lavoro](how-to-accelerate-alert-incident-response.md) 
 degli avvisi [Tipi di avviso e descrizioni](alert-engine-messages.md)
