---
title: Modifiche importanti previste per il Centro sicurezza di Azure
description: Prossime modifiche al Centro sicurezza di Azure che può essere opportuno conoscere e per le quali potrebbe essere necessaria una pianificazione
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/18/2021
ms.author: memildin
ms.openlocfilehash: b9a93286b6a546160b6c621d084437f671eab4d3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773573"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

| Modifica pianificata                                                                                                                                                        | Data stimata per la modifica |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Marzo 2021                |
| [Deprecazione di 11 avvisi di Azure Defender](#deprecation-of-11-azure-defender-alerts)                                                                                   | Marzo 2021                |
| [21 raccomandazioni che passano tra i controlli di sicurezza](#21-recommendations-moving-between-security-controls)                                                           | Aprile 2021                |
| [Altri due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti](#two-further-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | Aprile 2021                |
| [Le raccomandazioni di AWS verranno rilasciate per la disponibilità a livello generale (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | Aprile 2021                |
| [Miglioramenti alla raccomandazione di classificazione dei dati SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti 

**Data stimata per la modifica:** 2021 marzo

Le due raccomandazioni seguenti sono state pianificate per essere deprecate nel 2021 febbraio:

- **Per applicare gli aggiornamenti del sistema, è necessario riavviare i computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.
- **È necessario installare l'agente di monitoraggio nei computer**. Questa raccomandazione si riferisce solo ai computer locali e la relativa logica verrà trasferita a un'altra raccomandazione, **log Analytics problemi di integrità dell'agente devono essere risolti nei computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.

Si consiglia di controllare le configurazioni di esportazione continua e di automazione del flusso di lavoro per verificare se tali raccomandazioni sono incluse. Inoltre, tutti i dashboard o altri strumenti di monitoraggio che potrebbero utilizzarli devono essere aggiornati di conseguenza.

Per ulteriori informazioni su questi consigli, vedere la pagina di riferimento relativa alle [raccomandazioni sulla sicurezza](recommendations-reference.md).

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
 




### <a name="21-recommendations-moving-between-security-controls"></a>21 raccomandazioni che passano tra i controlli di sicurezza 

**Data stimata per la modifica:** Aprile 2021

Le indicazioni seguenti sono state spostate in un altro controllo di sicurezza. I controlli di sicurezza sono gruppi logici di raccomandazioni sulla sicurezza correlate e riflettono le superfici di attacco vulnerabili. Questo spostamento garantisce che ognuno di questi consigli si trovi nel controllo più appropriato per soddisfare l'obiettivo. 

Per informazioni sulle raccomandazioni disponibili in ogni controllo di sicurezza, vedere Controlli di sicurezza e relative raccomandazioni.

|Recommendation |Modifica e conseguenze  |
|---------|---------|
|La valutazione delle vulnerabilità deve essere abilitata nei server SQL<br>La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL<br>Le vulnerabilità nei database SQL devono essere risolte in un nuovo<br>Le vulnerabilità nei database SQL delle macchine virtuali devono essere corrette     |Passaggio dalle vulnerabilità di correzione (vale a 6 punti)<br>per correggere le configurazioni di sicurezza (vale a sua pena 4 punti).<br>A seconda dell'ambiente, questi consigli avranno un impatto ridotto sul punteggio.|
|Alla sottoscrizione deve essere assegnato più di un proprietario<br>Le variabili dell'account di automazione devono essere crittografate<br>Dispositivi tutto-il processo controllato ha interrotto l'invio degli eventi<br>Dispositivi tutto-errore di convalida della linea di base del sistema operativo<br>Dispositivi per l'aggiornamento del pacchetto di crittografia TLS necessari<br>Dispositivi Internet: porte aperte sul dispositivo<br>Dispositivi Internet delle cose: è stato trovato un criterio di firewall permissivo in una delle catene<br>Dispositivi Internet delle cose: è stata trovata una regola permissiva del firewall nella catena di input<br>Dispositivi Internet delle cose: è stata trovata una regola permissiva del firewall nella catena di output<br>È consigliabile abilitare i log di diagnostica nell'hub IoT<br>Dispositivi per l'invio di messaggi sottoutilizzati dall'agente<br>Dispositivi di Internet delle cose: i criteri di filtro IP predefiniti devono essere negati<br>Dispositivi Internet-Large-regola di filtro IP-intervallo IP<br>Dispositivi di Internet delle cose: le dimensioni e gli intervalli dei messaggi dell'agente devono essere modificati<br>Dispositivi Internet: credenziali di autenticazione identiche<br>Dispositivi di Internet delle cose: il processo controllato ha interrotto l'invio degli eventi<br>Dispositivi di Internet delle cose: la configurazione della linea di base del sistema operativo deve essere fissa|Passaggio per **implementare le procedure di sicurezza consigliate**.<br>Quando una raccomandazione passa al controllo di sicurezza implementa procedure consigliate per la sicurezza, che non vale la pena, la raccomandazione non influisca più sul punteggio sicuro.|
|||


### <a name="two-further-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Altri due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti

**Data stimata per la modifica:** Aprile 2021

Le due raccomandazioni seguenti sono deprecate:

- La **versione del sistema operativo deve essere aggiornata per i ruoli del servizio cloud** . per impostazione predefinita, Azure aggiorna periodicamente il sistema operativo guest all'immagine supportata più recente all'interno della famiglia di sistemi operativi specificata nella configurazione del servizio (con estensione cscfg), ad esempio Windows Server 2016.
- I **servizi di Kubernetes devono essere aggiornati a una versione di Kubernetes non vulnerabile** . Le valutazioni di questa raccomandazione non sono altrettanto ampie come quelle desiderate. La versione corrente di questa raccomandazione verrà sostituita con una versione migliorata che è meglio allineata alle esigenze di sicurezza del cliente.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Le raccomandazioni di AWS verranno rilasciate per la disponibilità a livello generale (GA)

**Data stimata per la modifica:** Aprile 2021

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Le raccomandazioni provenienti dall'hub di sicurezza AWS sono state in anteprima dopo l'introduzione dei connettori cloud. Le raccomandazioni contrassegnate come **Anteprima** non sono incluse nei calcoli del Punteggio sicuro, ma dovrebbero comunque essere risolte laddove possibile, in modo che, al termine del periodo di anteprima, contribuiscano al punteggio.

Con questa modifica, due set di raccomandazioni AWS vengono spostati in GA:

- [Controlli di PCI DSS dell'hub di sicurezza](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Controlli di benchmark per le fondamenta dell'hub di sicurezza CIS AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Quando si tratta di GA e le valutazioni vengono eseguite sulle risorse di AWS, i risultati influirà sul punteggio sicuro combinato per tutte le risorse cloud ibride e multipiattaforma. 



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Miglioramenti alla raccomandazione di classificazione dei dati SQL

**Data stimata per la modifica:** Q2 2021

I **dati sensibili ai consigli nei database SQL devono essere classificati** nel controllo **applica sicurezza classificazione dati** verranno sostituiti con una nuova versione più allineata alla strategia di classificazione dei dati di Microsoft. Di conseguenza, l'ID della raccomandazione cambierà (attualmente b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Passaggi successivi

Per tutte le modifiche recenti al prodotto, vedere [Novità del Centro sicurezza di Azure](release-notes.md).