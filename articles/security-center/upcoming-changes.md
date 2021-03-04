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
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6bec9f0a1c22691d818566cec3f59c1ec0f3d3bb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051617"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

- [Le raccomandazioni di AWS verranno rilasciate per la disponibilità a livello generale (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Due consigli legacy non scriveranno più dati direttamente nel log attività di Azure](#two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log)
- [Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Miglioramenti alla raccomandazione di classificazione dei dati SQL](#enhancements-to-sql-data-classification-recommendation)
- [Deprecazione di 11 avvisi di Azure Defender](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Le raccomandazioni di AWS verranno rilasciate per la disponibilità a livello generale (GA)

**Data stimata per la modifica:** Aprile 2021

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Le raccomandazioni provenienti dall'hub di sicurezza AWS sono state in anteprima dopo l'introduzione dei connettori cloud. Le raccomandazioni contrassegnate come **Anteprima** non sono incluse nei calcoli del Punteggio sicuro, ma dovrebbero comunque essere risolte laddove possibile, in modo che, al termine del periodo di anteprima, contribuiscano al punteggio.

Con questa modifica, due set di raccomandazioni AWS vengono spostati in GA:

- [Controlli di PCI DSS dell'hub di sicurezza](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Controlli di benchmark per le fondamenta dell'hub di sicurezza CIS AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Quando si tratta di GA e le valutazioni vengono eseguite sulle risorse di AWS, i risultati influirà sul punteggio sicuro combinato per tutte le risorse cloud ibride e multipiattaforma. 



### <a name="two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log"></a>Due consigli legacy non scriveranno più dati direttamente nel log attività di Azure 

**Data stimata per la modifica:** 2021 marzo

Il Centro sicurezza passa i dati per quasi tutte le raccomandazioni sulla sicurezza a Azure Advisor che, a sua volta, li scrive nel [log attività di Azure](../azure-monitor/essentials/activity-log.md).

Per due consigli, i dati vengono scritti simultaneamente direttamente nel log attività di Azure. Con questa modifica, il Centro sicurezza smetterà di scrivere i dati per queste raccomandazioni di sicurezza legacy direttamente nel log attività. Al contrario, i dati verranno esportati in Azure Advisor come per tutti gli altri consigli. 

Le due raccomandazioni legacy sono:
- È consigliabile risolvere i problemi di integrità di Endpoint Protection nei computer
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte

Se è stato effettuato l'accesso alle informazioni per questi due consigli nella categoria "raccomandazione di tipo TaskDiscovery" del log attività, questo non sarà più disponibile.

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti 

**Data stimata per la modifica:** 2021 marzo

Le due raccomandazioni seguenti sono state pianificate per essere deprecate nel 2021 febbraio:

- **Per applicare gli aggiornamenti del sistema, è necessario riavviare i computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.
- **È necessario installare l'agente di monitoraggio nei computer**. Questa raccomandazione si riferisce solo ai computer locali e la relativa logica verrà trasferita a un'altra raccomandazione, **log Analytics problemi di integrità dell'agente devono essere risolti nei computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.

Si consiglia di controllare le configurazioni di esportazione continua e di automazione del flusso di lavoro per verificare se tali raccomandazioni sono incluse. Inoltre, tutti i dashboard o altri strumenti di monitoraggio che potrebbero utilizzarli devono essere aggiornati di conseguenza.

Per ulteriori informazioni su questi consigli, vedere la pagina di riferimento relativa alle [raccomandazioni sulla sicurezza](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Miglioramenti alla raccomandazione di classificazione dei dati SQL

**Data stimata per la modifica:** Q2 2021

I **dati sensibili ai consigli nei database SQL devono essere classificati** nel controllo **applica sicurezza classificazione dati** verranno sostituiti con una nuova versione più allineata alla strategia di classificazione dei dati di Microsoft. Di conseguenza, l'ID della raccomandazione cambierà (attualmente b0df6f56-862d-4730-8597-38c0fd4ebd59).


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
