---
title: Modifiche importanti previste per il Centro sicurezza di Azure
description: Prossime modifiche al Centro sicurezza di Azure che può essere opportuno conoscere e per le quali potrebbe essere necessaria una pianificazione
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 375e8a748e8833e9483d92353ed04add287e90fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705093"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

- [Due consigli legacy non scriveranno più dati direttamente nel log attività di Azure](#two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log)
- [Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Miglioramenti alla raccomandazione di classificazione dei dati SQL](#enhancements-to-sql-data-classification-recommendation)
- [Deprecazione di 11 avvisi di Azure Defender](#deprecation-of-11-azure-defender-alerts)


### <a name="two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log"></a>Due consigli legacy non scriveranno più dati direttamente nel log attività di Azure 

**Data stimata per la modifica:** 2021 marzo

Il Centro sicurezza passa i dati per quasi tutte le raccomandazioni sulla sicurezza a Azure Advisor che, a sua volta, li scrive nel [log attività di Azure](../azure-monitor/essentials/activity-log.md).

Per due consigli, i dati vengono scritti simultaneamente direttamente nel log attività di Azure. Con questa modifica, il Centro sicurezza smetterà di scrivere i dati per queste raccomandazioni di sicurezza legacy direttamente nel log attività. Al contrario, i dati verranno esportati in Azure Advisor come per tutti gli altri consigli. 

Le due raccomandazioni legacy sono:
- È consigliabile risolvere i problemi di integrità di Endpoint Protection nei computer
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte

Se è stato effettuato l'accesso alle informazioni per questi due consigli nella categoria "raccomandazione di tipo TaskDiscovery" del log attività, questo non sarà più disponibile.

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti 

**Data stimata per la modifica:** 2021 febbraio

Le due raccomandazioni seguenti sono state pianificate per essere deprecate nel 2021 febbraio:

- **Per applicare gli aggiornamenti del sistema, è necessario riavviare i computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.
- **È necessario installare l'agente di monitoraggio nei computer**. Questa raccomandazione si riferisce solo ai computer locali e la relativa logica verrà trasferita a un'altra raccomandazione, **log Analytics problemi di integrità dell'agente devono essere risolti nei computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.

Si consiglia di controllare le configurazioni di esportazione continua e di automazione del flusso di lavoro per verificare se tali raccomandazioni sono incluse. Inoltre, tutti i dashboard o altri strumenti di monitoraggio che potrebbero utilizzarli devono essere aggiornati di conseguenza.

Per ulteriori informazioni su questi consigli, vedere la pagina di riferimento relativa alle [raccomandazioni sulla sicurezza](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Miglioramenti alla raccomandazione di classificazione dei dati SQL

**Data stimata per la modifica:** Q2 2021

I **dati sensibili ai consigli nei database SQL devono essere classificati** nel controllo **applica sicurezza classificazione dati** verranno sostituiti con una nuova versione più allineata alla strategia di classificazione dei dati di Microsoft. Di conseguenza, anche l'ID dell'indicazione cambierà (attualmente b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>Deprecazione di 11 avvisi di Azure Defender

**Data stimata per la modifica:** 2021 marzo

Il mese prossimo, gli undici avvisi di Azure Defender elencati di seguito saranno deprecati.

- I nuovi avvisi sostituiranno questi due avvisi e forniranno una copertura migliore:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | ANTEPRIMA: è stata rilevata l'esecuzione della funzione "Get-AzureDomainInfo" del Toolkit microrompi |
    | ARM_MicroBurstRunbook    | ANTEPRIMA: è stata rilevata l'esecuzione della funzione "Get-AzurePasswords" del Toolkit microrompi  |
    |                          |                                                                          |

- Questi nove avvisi sono correlati a un connettore Azure Active Directory Identity Protection che è già stato deprecato:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Proprietà di accesso insolite |
    | AnonymousLogin      | Indirizzo IP anonimo          |
    | InfectedDeviceLogin | Indirizzo IP collegato a malware     |
    | ImpossibleTravel    | Trasferimento atipico               |
    | MaliciousIP         | Indirizzo IP dannoso          |
    | LeakedCredentials   | Credenziali perse            |
    | PasswordSpray       | Spray password                |
    | LeakedCredentials   | Intelligence per le minacce di Azure AD  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Passaggi successivi

Per tutte le modifiche recenti al prodotto, vedere [Novità del Centro sicurezza di Azure](release-notes.md).
