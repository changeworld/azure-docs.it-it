---
title: Note sulla versione per il Centro sicurezza di Azure
description: Descrizione delle novità e delle modifiche apportate al Centro sicurezza di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2021
ms.author: memildin
ms.openlocfilehash: d171e3a7f1f0d63d0a5d130c6e19b246bf0cd795
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661815"
---
# <a name="whats-new-in-azure-security-center"></a>Novità del Centro sicurezza di Azure

Il Centro sicurezza è in fase di sviluppo attivo e riceve regolarmente miglioramenti. Per stare al passo con gli sviluppi più recenti, questa pagina fornisce informazioni su funzionalità nuove e deprecate, oltre che sulle correzioni dei bug.

Questa pagina viene aggiornata regolarmente, quindi è consigliabile consultarla spesso. 

Per informazioni sulle modifiche *pianificate* che saranno presto disponibili nel Centro sicurezza, vedere [Modifiche importanti che interesseranno il Centro sicurezza di Azure](upcoming-changes.md). 

> [!TIP]
> Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarle in [Archive for What's new in Azure Security Center](release-notes-archive.md) (Archivio per le novità nel Centro sicurezza di Azure).


## <a name="january-2021"></a>Gennaio 2021

Gli aggiornamenti di dicembre includono:

- [La valutazione della vulnerabilità per computer locali e più cloud viene rilasciata per la disponibilità a livello generale](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [L'API per il Punteggio sicuro è stata rilasciata per la disponibilità generale (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Esportazione in CSV dell'elenco filtrato di raccomandazioni](#csv-export-of-filtered-list-of-recommendations)
- [Le risorse "non applicabili" sono ora segnalate come "conformi" nelle valutazioni dei criteri di Azure](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)


### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>La valutazione della vulnerabilità per computer locali e più cloud viene rilasciata per la disponibilità a livello generale

A ottobre è stata annunciata un'anteprima per l'analisi dei server con abilitazione di Azure Arc con lo strumento di analisi integrato per la valutazione delle vulnerabilità di [Azure Defender per i server](defender-for-servers-introduction.md) (con tecnologia Qualys).

È ora disponibile a livello generale (GA).

Quando Azure Arc viene abilitato in computer non di Azure, il Centro sicurezza offre la possibilità di distribuirvi lo strumento integrato di analisi delle vulnerabilità, manualmente e su larga scala.

Con questo aggiornamento, è possibile sfruttare la potenza di **Azure Defender per i server** per consolidare il programma di gestione delle vulnerabilità in tutti gli asset di Azure e non di Azure.

Funzionalità principali:

- Monitoraggio dello stato di provisioning dello strumento di analisi per la valutazione delle vulnerabilità nei computer Azure Arc
- Provisioning dell'agente di valutazione delle vulnerabilità nei computer Azure Arc Windows e Linux non protetti (manualmente e su larga scala)
- Ricezione e analisi delle vulnerabilità rilevate dagli agenti distribuiti (manualmente e su larga scala)
- Esperienza unificata per macchine virtuali di Azure e computer Azure Arc

[Altre informazioni sulla distribuzione dello strumento di analisi delle vulnerabilità integrato nei computer ibridi](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Altre informazioni sui server con abilitazione di Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-api-is-released-for-general-availability-ga"></a>L'API per il Punteggio sicuro è stata rilasciata per la disponibilità generale (GA)

È ora possibile accedere al Punteggio tramite l' [API per il Punteggio sicuro](/rest/api/securitycenter/securescores/). I metodi dell'API offrono la flessibilità necessaria per eseguire query nei dati e creare un meccanismo personalizzato per la creazione di report sui punteggi di sicurezza nel tempo. È ad esempio possibile usare l'API **Secure Score** per ottenere il punteggio per una sottoscrizione specifica. È anche possibile usare l'API **Secure Score Controls** per elencare i controlli di sicurezza e il punteggio corrente delle sottoscrizioni.

Per esempi di strumenti esterni consentiti dall'API Secure Score, vedere l'[area relativa a Secure Score della community di GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Altre informazioni sul [punteggio di sicurezza e i controlli di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md).


### <a name="csv-export-of-filtered-list-of-recommendations"></a>Esportazione in CSV dell'elenco filtrato di raccomandazioni 

A novembre 2020 sono stati aggiunti i filtri alla pagina Raccomandazioni (vedere [L'elenco di raccomandazioni ora include i filtri](#recommendations-list-now-includes-filters)). A dicembre questi filtri sono stati ampliati ([La pagina Raccomandazioni contiene nuovi filtri per ambiente, gravità e risposte disponibili](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Con questo annuncio, il comportamento del **pulsante Scarica in CSV** è stato cambiato in modo che l'esportazione in CSV includa solo le raccomandazioni attualmente visualizzate nell'elenco filtrato. 

Nell'immagine seguente, ad esempio, è possibile vedere che l'elenco è stato filtrato per includere due raccomandazioni. Il file CSV generato include i dettagli sullo stato di ogni risorsa interessata da queste due raccomandazioni.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Esportazione di raccomandazioni filtrate in un file CSV":::

Per altre informazioni, vedere [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Le risorse "non applicabili" sono ora segnalate come "conformi" nelle valutazioni dei criteri di Azure

In precedenza, le risorse valutate per una raccomandazione e rilevate come **non applicabili** sono visualizzate in criteri di Azure come "non conformi". Nessuna azione dell'utente potrebbe modificare il proprio stato in "conforme". Con questa modifica, questi elementi vengono segnalati come "conformi" per una maggiore chiarezza.

L'unico effetto si vedrà in Criteri di Azure, dove il numero di risorse conformi aumenterà. Non ci sarà alcun impatto sul punteggio di sicurezza nel Centro sicurezza di Azure.



## <a name="december-2020"></a>Dicembre 2020

Gli aggiornamenti di dicembre includono:

- [Azure Defender per server SQL nei computer è disponibile a livello generale](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Il supporto di Azure Defender per SQL per i pool SQL dedicati di Azure Synapse Analytics è disponibile a livello generale](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Gli amministratori globali ora possono concedere autorizzazioni a livello di tenant a se stessi](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Due nuovi piani di Azure Defender: Azure Defender per DNS e Azure Defender per Resource Manager (in anteprima)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Pagina Nuovi avvisi di sicurezza nel portale di Azure (anteprima)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Esperienza del Centro sicurezza ottimizzata per Database SQL di Azure e Istanza gestita di SQL](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Strumenti e filtri dell'inventario degli asset aggiornati](#asset-inventory-tools-and-filters-updated)
- [La raccomandazione relativa alla richiesta di certificati SSL da parte delle app Web non fa più parte del punteggio di sicurezza](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [La pagina Raccomandazioni contiene nuovi filtri per ambiente, gravità e risposte disponibili](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [L'esportazione continua ha nuovi tipi di dati e criteri DeployIfNotExists migliorati](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender per server SQL nei computer è disponibile a livello generale

Il Centro sicurezza di Azure prevede due piani di Azure Defender per SQL Server:

- **Azure Defender per i server di database SQL di Azure**: protegge i sistemi SQL Server nativi di Azure 
- **Azure Defender per i server SQL nei computer**: estende le stesse protezioni ai server SQL in ambienti ibridi, multicloud e locali

Con questo annuncio, **Azure Defender per SQL** ora protegge i database e i relativi dati ovunque siano collocati.

Azure Defender per SQL include funzionalità di valutazione delle vulnerabilità. Lo strumento di valutazione delle vulnerabilità offre le seguenti funzionalità avanzate:

- **Configurazione di base** (novità) per affinare in modo intelligente i risultati delle analisi di vulnerabilità limitandoli a quelli che potrebbero rappresentare problemi concreti per la sicurezza. Dopo aver stabilito lo stato di sicurezza di base, lo strumento di valutazione delle vulnerabilità segnala solo le deviazioni da tale stato. I risultati corrispondenti alla base non vengono considerati problemi nelle analisi successive. In questo modo gli analisti possono dedicare la loro attenzione alle questioni importanti.
- **Informazioni di benchmark dettagliate** per aiutare a *interpretare* i risultati individuati e al motivo per cui riguardano le risorse.
- **Script di correzione** per mitigare i rischi identificati.

Vedere altre informazioni su [Azure Defender per SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Il supporto di Azure Defender per SQL per i pool SQL dedicati di Azure Synapse Analytics è disponibile a livello generale

Azure Synapse Analytics (in precedenza SQL Data Warehouse) è un servizio di analisi che riunisce funzionalità aziendali di data warehousing e analisi di Big Data. I pool SQL dedicati rappresentano le funzionalità di data warehousing aziendali di Azure Synapse. Per altre informazioni, vedere [Che cos'è Azure Synapse Analytics (in precedenza SQL Data Warehouse)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Azure Defender per SQL protegge i pool SQL dedicati con:

- **Advanced Threat Protection** per rilevare minacce e attacchi 
- **Funzionalità di valutazione delle vulnerabilità** per identificare e correggere le configurazioni di sicurezza errate

Il supporto di Azure Defender per SQL per i pool SQL di Azure Synapse Analytics viene aggiunto automaticamente al bundle di database SQL di Azure nel Centro sicurezza di Azure. Nella pagina dell'area di lavoro di Synapse nel portale di Azure sarà disponibile una nuova scheda "Azure Defender per SQL".

Vedere altre informazioni su [Azure Defender per SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Gli amministratori globali ora possono concedere autorizzazioni a livello di tenant a se stessi

Un utente con il ruolo **Amministratore globale** di Azure Active Directory potrebbe avere responsabilità a livello di tenant ma non avere le autorizzazioni di Azure per visualizzare queste informazioni nell'intera organizzazione in Azure Active Directory. 

Per assegnare a se stessi le autorizzazioni a livello di tenant, seguire le istruzioni riportate in [Concedere autorizzazioni a livello di tenant a se stessi](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Due nuovi piani di Azure Defender: Azure Defender per DNS e Azure Defender per Resource Manager (in anteprima)

Sono state aggiunte due nuove funzionalità native del cloud per la protezione completa dell'ambiente di Azure dalle minacce.

Queste due nuove funzionalità migliorano notevolmente la resilienza dell'ambiente contro attacchi e minacce, oltre ad aumentare sensibilmente il numero di risorse di Azure protette da Azure Defender.

- **Azure Defender per Resource Manager** monitora automaticamente tutte le operazioni di gestione risorse eseguite nell'organizzazione. Per altre informazioni, vedere:
    - [Introduzione ad Azure Defender per Resource Manager](defender-for-resource-manager-introduction.md)
    - [Rispondere agli avvisi di Azure Defender per Resource Manager](defender-for-resource-manager-usage.md)
    - [Elenco degli avvisi di Azure Defender per Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender per DNS** monitora continuamente tutte le query DNS delle risorse di Azure. Per altre informazioni, vedere:
    - [Introduzione ad Azure Defender per DNS](defender-for-dns-introduction.md)
    - [Rispondere agli avvisi di Azure Defender per DNS](defender-for-dns-usage.md)
    - [Elenco degli avvisi di Azure Defender per DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Pagina Nuovi avvisi di sicurezza nel portale di Azure (anteprima)

La pagina Avvisi di sicurezza del Centro sicurezza di Azure è stata riprogettata per offrire:

- **Esperienza di valutazione migliorata per gli avvisi**: per ridurre il sovraccarico di avvisi ed evidenziare più facilmente le minacce pertinenti, l'elenco include filtri personalizzabili e opzioni di raggruppamento
- **Più informazioni nell'elenco di avvisi**, ad esempio tattiche MITRE ATT&ACK
- **Pulsante per creare avvisi di esempio**: per valutare le funzionalità di Azure Defender e testare la configurazione degli avvisi (per verificare l'integrazione di SIEM, le notifiche tramite posta elettronica e le automazioni del flusso di lavoro), è possibile creare avvisi di esempio in tutti i piani di Azure Defender
- **Allineamento con l'esperienza degli eventi imprevisti di Azure Sentinel**: per i clienti che usano entrambi i prodotti, è ora più semplice passare da uno all'altro e combinare le informazioni che offrono
- **Prestazioni più elevate** per elenchi di avvisi lunghi
- **Esplorazione tramite tastiera** dell'elenco di avvisi
- **Avvisi di Azure Resource Graph**: è possibile eseguire query sugli avvisi in Azure Resource Graph, l'API di tipo Kusto per tutte le risorse. Questa funzionalità è utile anche per creare dashboard di avvisi personalizzati. Vedere [altre informazioni su Azure Resource Graph](../governance/resource-graph/index.yml).

Per accedere alla nuova esperienza, usare il collegamento 'Prova adesso' sul banner nella parte superiore della pagina Avvisi di sicurezza.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner con il collegamento alla nuova esperienza in anteprima per gli avvisi":::

Per creare avvisi di esempio nella nuova esperienza, vedere [Generare avvisi di esempio di Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Esperienza del Centro sicurezza ottimizzata per Database SQL di Azure e Istanza gestita di SQL 

L'esperienza del Centro sicurezza in SQL consente di accedere alle funzionalità del Centro sicurezza e di Azure Defender per SQL descritte di seguito:

- **Raccomandazioni sulla sicurezza**: il Centro sicurezza analizza periodicamente lo stato di sicurezza di tutte le risorse di Azure connesse per identificare possibili errori di configurazione della sicurezza. Fornisce quindi raccomandazioni su come correggere queste vulnerabilità e migliorare il comportamento di sicurezza delle organizzazioni.
- **Avvisi di sicurezza**: servizio di rilevamento che monitora costantemente le attività di Azure SQL per individuare minacce come attacchi SQL injection, attacchi di forza bruta e abuso dei privilegi. Questo servizio attiva avvisi di sicurezza dettagliati e orientati all'azione nel Centro sicurezza e fornisce opzioni per continuare le analisi con Azure Sentinel, la soluzione SIEM nativa di Microsoft Azure.
- **Risultati**: servizio di valutazione delle vulnerabilità che monitora continuamente le configurazioni di Azure SQL e aiuta a correggere le vulnerabilità. Le analisi di valutazione forniscono una panoramica degli stati di sicurezza di Azure SQL insieme a risultati dettagliati sulla sicurezza.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Le funzionalità di sicurezza del Centro sicurezza di Azure per SQL sono disponibili all'interno di Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Strumenti e filtri dell'inventario degli asset aggiornati

La pagina di inventario nel Centro sicurezza di Azure è stata aggiornata con le modifiche seguenti:

- **Guide e feedback** aggiunti alla barra degli strumenti. Viene aperto un riquadro con collegamenti a informazioni e strumenti correlati. 
- **Filtro per le sottoscrizioni** aggiunto ai filtri predefiniti disponibili per le risorse.
- Collegamento **Apri query** per aprire le opzioni di filtro correnti come query di Azure Resource Graph (in precedenza "Visualizza in Resource Graph Explorer").
- **Opzioni per gli operatori** per ogni filtro. Ora è possibile scegliere altri operatori logici oltre a "=". Ad esempio, è possibile trovare tutte le risorse con raccomandazioni attive il cui titolo include la stringa "crittografare". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Controlli dell'opzione Operatore nei filtri dell'inventario degli asset":::

Per altre informazioni sull'inventario, vedere [Esplorare e gestire le risorse con l'inventario degli asset](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>La raccomandazione relativa alla richiesta di certificati SSL da parte delle app Web non fa più parte del punteggio di sicurezza

La raccomandazione "È consigliabile che le app Web richiedano un certificato SSL per tutte le richieste in ingresso" è stata spostata dal controllo di sicurezza **Gestisci l'accesso e le autorizzazioni** (che vale un massimo di 4 punti) al controllo **Implementa le procedure consigliate per la sicurezza** (che non vale alcun punto). 

L'impostazione della richiesta di un certificato da parte delle app Web le rende certamente più sicure. Questa impostazione è tuttavia irrilevante per le app Web pubbliche. se si accede al sito tramite HTTP e non HTTPS, non si riceveranno i certificati client. Pertanto, se l'applicazione richiede i certificati client, è consigliabile non consentire le richieste all'applicazione tramite HTTP. Per altre informazioni, vedere [Configurare l'autenticazione reciproca TLS per Servizio app di Azure](../app-service/app-service-web-configure-tls-mutual-auth.md).

Con questa modifica, la raccomandazione è ora una procedura consigliata che non influisce sul punteggio. 

Per informazioni sulle raccomandazioni disponibili in ogni controllo di sicurezza, vedere [Controlli di sicurezza e relative raccomandazioni](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>La pagina Raccomandazioni contiene nuovi filtri per ambiente, gravità e risposte disponibili

Il Centro sicurezza di Azure monitora tutte le risorse connesse e genera raccomandazioni sulla sicurezza. Usare queste raccomandazioni per rafforzare il comportamento del cloud ibrido e monitorare la conformità con i criteri e gli standard pertinenti per la propria organizzazione, il proprio settore e il proprio paese.

L'elenco delle raccomandazioni sulla sicurezza cresce ogni mese, man mano che il Centro sicurezza continua a espandere la propria copertura e le funzionalità. Ad esempio, vedere [Aggiunte 29 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Man mano che l'elenco cresce, aumenta anche l'esigenza di applicare filtri per visualizzare le raccomandazioni di maggiore interesse. Lo scorso novembre sono stati aggiunti i filtri alla pagina Raccomandazioni (vedere [L'elenco di raccomandazioni ora include i filtri](#recommendations-list-now-includes-filters)).

I filtri aggiunti questo mese forniscono le opzioni per affinare l'elenco delle raccomandazioni in base a:

- **Ambiente**: visualizzare le raccomandazioni relative alle risorse di AWS, GCP o Azure (o qualsiasi combinazione)
- **Gravità**: visualizzare le raccomandazioni in base alla classificazione di gravità impostata dal Centro sicurezza
- **Azioni di risposta**: visualizzare le raccomandazioni in base alla disponibilità delle opzioni di risposta del Centro sicurezza: Correzione rapida, Nega e Imponi

    > [!TIP]
    > Il filtro delle azioni di risposta sostituisce il filtro **Correzione rapida disponibile (Sì/No)** . 
    > 
    > Per altre informazioni su ognuna di queste opzioni di risposta, vedere:
    > - [Correzione rapida](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Impedire gli errori di configurazione con i consigli di tipo Imponi/Nega](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Raccomandazioni raggruppate per controllo di sicurezza" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>L'esportazione continua ha nuovi tipi di dati e criteri DeployIfNotExists migliorati

Gli strumenti di esportazione continua del Centro sicurezza di Azure consentono di esportare le raccomandazioni e gli avvisi del Centro sicurezza per usarli con altri strumenti di monitoraggio nell'ambiente.

L'esportazione continua consente di personalizzare completamente gli elementi che verranno esportati e la posizione in cui verranno inseriti. Per informazioni complete, vedere [Esportazione continua dei dati del Centro sicurezza](continuous-export.md).

Questi strumenti sono stati migliorati e ampliati nei modi seguenti:

- **I criteri DeployIfNotExists dell'esportazione continua sono stati migliorati**. Ora consentono di:

    - **Verificare se la configurazione è abilitata.** Se non è abilitata, il criterio visualizza uno stato non conforme e crea una risorsa conforme. Per altre informazioni sui modelli di Criteri di Azure forniti, vedere la scheda "Distribuire su larga scala con Criteri di Azure" in [Configurare un'esportazione continua](continuous-export.md#set-up-a-continuous-export).

    - **Supportare l'esportazione dei risultati di sicurezza.** Usando i modelli di Criteri di Azure è possibile configurare l'esportazione continua in modo che includa i risultati. Questa configurazione è importante quando si esportano raccomandazioni che contengono "sottoraccomandazioni", ad esempio i risultati di analisi di valutazione delle vulnerabilità o aggiornamenti di sistema specifici per la raccomandazione "padre" "È consigliabile installare gli aggiornamenti del sistema nei computer".
    
    - **Supportare l'esportazione dei dati dei punteggi di sicurezza.**

- **Dati di valutazione della conformità con le normative aggiunti (in anteprima).** È ora possibile eseguire l'esportazione continua degli aggiornamenti alle valutazioni di conformità con le normative, ad esempio per eventuali iniziative personalizzate, in un'area di lavoro Log Analytics o in un hub eventi. Questa funzionalità non è disponibile nei cloud nazionali/sovrani.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Opzioni per l'inclusione delle informazioni sulla valutazione della conformità con le normative con i dati di esportazione continua.":::


## <a name="november-2020"></a>Novembre 2020

Gli aggiornamenti del mese di novembre includono quanto segue:

- [Aggiunte 29 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Aggiunta di NIST SP 800 171 R2 al dashboard di conformità alle normative del Centro sicurezza](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [L'elenco di raccomandazioni ora include i filtri](#recommendations-list-now-includes-filters)
- [Esperienza di provisioning automatico migliorata e ampliata](#auto-provisioning-experience-improved-and-expanded)
- [Punteggio di sicurezza ora disponibile nell'esportazione continua (anteprima)](#secure-score-is-now-available-in-continuous-export-preview)
- [Aggiunta di raccomandazioni secondarie alla raccomandazione "Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali"](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [La pagina di gestione dei criteri nel portale di Azure ora mostra lo stato delle assegnazioni di criteri predefiniti](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Aggiunte 29 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark

Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md).

Le seguenti 29 nuove raccomandazioni di anteprima verranno aggiunte al Centro sicurezza per aumentare la copertura del benchmark.

Le raccomandazioni in anteprima non contrassegnano una risorsa come non integra e non sono incluse nei calcoli del punteggio di sicurezza. Correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima. Per altre informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

| Controllo di sicurezza                     | Nuove raccomandazioni                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Crittografa i dati in transito              | - Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL<br>- Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL<br>- TLS deve essere aggiornato all'ultima versione per l'app per le API<br>- TLS deve essere aggiornato all'ultima versione per l'app per le funzioni<br>- TLS deve essere aggiornato all'ultima versione per l'app Web<br>- L'app per le API deve usare FTPS<br>- L'app per le funzioni deve usare FTPS<br>- L'app Web deve usare FTPS                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gestire l'accesso e le autorizzazioni        | - Le app Web devono richiedere un certificato SSL per tutte le richieste in ingresso<br>- Nell'app per le API è necessario usare un'identità gestita<br>- Nell'app per le funzioni è necessario usare un'identità gestita<br>- Nell'app Web è necessario usare un'identità gestita                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Limita l'accesso non autorizzato alla rete | - L'endpoint privato deve essere abilitato per i server PostgreSQL<br>- L'endpoint privato deve essere abilitato per i server MariaDB<br>- L'endpoint privato deve essere abilitato per i server MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Abilita il controllo e la registrazione          | - È necessario abilitare i log di diagnostica in Servizi app                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementa le procedure consigliate per la sicurezza    | - La soluzione Backup di Azure deve essere abilitata per le macchine virtuali<br>- Il backup con ridondanza geografica deve essere abilitato per Database di Azure per MariaDB<br>- Il backup con ridondanza geografica deve essere abilitato per i database di Azure per MySQL<br>- Il backup con ridondanza geografica deve essere abilitato per Database di Azure per PostgreSQL<br>- PHP deve essere aggiornato all'ultima versione per l'app per le API<br>- PHP deve essere aggiornato all'ultima versione per l'app Web<br>- Java deve essere aggiornato all'ultima versione per l'app per le API<br>- Java deve essere aggiornato all'ultima versione per l'app per le funzioni<br>- Java deve essere aggiornato all'ultima versione per l'app Web<br>- Python deve essere aggiornato all'ultima versione per l'app per le API<br>- Python deve essere aggiornato all'ultima versione per l'app per le funzioni<br>- Python deve essere aggiornato all'ultima versione per l'app Web<br>- La conservazione dei controlli per i server SQL deve essere impostata su almeno 90 giorni |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Collegamenti correlati:

- [Altre informazioni su Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Altre informazioni sulle app per le API di Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Altre informazioni sulle app per le funzioni di Azure](../azure-functions/functions-overview.md)
- [Altre informazioni sulle app Web di Azure](../app-service/overview.md)
- [Altre informazioni su Database di Azure per MariaDB](../mariadb/overview.md)
- [Altre informazioni su Database di Azure per MySQL](../mysql/overview.md)
- [Altre informazioni su Database di Azure per PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Aggiunta di NIST SP 800 171 R2 al dashboard di conformità alle normative del Centro sicurezza

Lo standard NIST SP 800-171 R2 è ora disponibile come iniziativa predefinita per l'uso nel dashboard di conformità alle normative del Centro sicurezza di Azure. I mapping per i controlli sono descritti in [Dettagli dell'iniziativa predefinita di conformità alle normative per NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Per applicare lo standard alle sottoscrizioni e monitorare continuamente lo stato di conformità, attenersi alle istruzioni riportate in [Personalizzazione del set di standard nel dashboard di conformità alle normative](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard NIST SP 800 171 R2 nel dashboard di conformità alle normative del Centro sicurezza":::

Per altre informazioni su questo standard di conformità, vedere [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>L'elenco di raccomandazioni ora include i filtri

È ora possibile filtrare l'elenco di raccomandazioni di sicurezza in base a una serie di criteri. Nell'esempio seguente l'elenco di raccomandazioni è stato filtrato per mostrare le raccomandazioni che:

- Sono **disponibili a livello generale** (ovvero, non in anteprima)
- Riguardano gli **account di archiviazione**
- Supportano la **correzione rapida**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtri per l'elenco di raccomandazioni":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Esperienza di provisioning automatico migliorata e ampliata

La funzionalità di provisioning automatico consente di ridurre il sovraccarico di gestione installando le estensioni necessarie nelle VM di Azure nuove ed esistenti in modo che possano trarre vantaggio dalle protezioni del Centro sicurezza. 

Con la crescita del Centro sicurezza di Azure, sono state sviluppate più estensioni ed è ora possibile monitorare un elenco più lungo di tipi di risorsa. Gli strumenti di provisioning automatico sono stati ampliati per supportare estensioni e tipi di risorsa aggiuntivi sfruttando le funzionalità di Criteri di Azure.

È ora possibile configurare il provisioning automatico di:

- Agente di Log Analytics
- (Novità) Componente aggiuntivo Criteri di Azure per Kubernetes
- (Novità) Microsoft Dependency Agent

Per altre informazioni, vedere [Provisioning automatico di agenti ed estensioni del Centro sicurezza di Azure](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Punteggio di sicurezza ora disponibile nell'esportazione continua (anteprima)

Con l'esportazione continua del punteggio di sicurezza, è possibile trasmettere le modifiche del punteggio in tempo reale a Hub eventi di Azure o a un'area di lavoro Log Analytics. Usare questa funzionalità per:

- Tenere traccia del punteggio di sicurezza nel corso del tempo con report dinamici
- Esportare i dati sul punteggio di sicurezza in Azure Sentinel (o qualsiasi altro strumento SIEM)
- Integrare questi dati con qualsiasi processo che potrebbe già essere in uso per monitorare il punteggio di sicurezza nell'organizzazione

Per altre informazioni, vedere [Esportazione continua dei dati del Centro sicurezza](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>Aggiunta di raccomandazioni secondarie alla raccomandazione "Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali"

La raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali** è stata ottimizzata. La nuova versione include raccomandazioni secondarie per ogni aggiornamento mancante e include i miglioramenti seguenti:

- Un'esperienza riprogettata nelle pagine del Centro sicurezza di Azure del portale di Azure. La pagina di dettagli della raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali** include l'elenco di risultati, come illustrato di seguito. Quando si seleziona un singolo risultato, viene visualizzato il riquadro dei dettagli con un collegamento alle informazioni sulla correzione e un elenco delle risorse interessate.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Apertura di una delle raccomandazioni secondarie nell'esperienza del portale per la raccomandazione aggiornata":::

- Dati della raccomandazione arricchiti da Azure Resource Graph. Azure Resource Graph è un servizio di Azure progettato per offrire un'esplorazione efficiente delle risorse. È possibile usare Azure Resource Graph per eseguire query su larga scala su un determinato set di sottoscrizioni, in modo da regolamentare efficacemente l'ambiente. 

    Per il Centro sicurezza di Azure, è possibile usare Azure Resource Graph e il [linguaggio di query Kusto](/azure/data-explorer/kusto/query/) per eseguire query su un'ampia gamma di dati relativi alla postura di sicurezza.

    In precedenza, se si eseguivano query su questa raccomandazione in Azure Resource Graph, le uniche informazioni disponibili erano che era necessario apportare la correzione raccomandata in un computer. La query seguente della versione ottimizzata restituirà tutti gli aggiornamenti di sistema mancanti raggruppati in base al computer.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>La pagina di gestione dei criteri nel portale di Azure ora mostra lo stato delle assegnazioni di criteri predefiniti

È ora possibile verificare se alle sottoscrizioni sono assegnati o meno i criteri predefiniti del Centro sicurezza nella relativa pagina **criteri di sicurezza** del portale di Azure.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="La pagina di gestione dei criteri del Centro sicurezza di Azure mostra le assegnazioni di criteri predefiniti":::

## <a name="october-2020"></a>Ottobre 2020

Gli aggiornamenti del mese di ottobre includono quanto segue:
- [Valutazione delle vulnerabilità per computer locali e multicloud (anteprima)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Aggiunta una raccomandazione per Firewall di Azure (anteprima)](#azure-firewall-recommendation-added-preview)
- [Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes - Aggiornata la raccomandazione con una correzione rapida](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Il dashboard Conformità con le normative include ora un'opzione per rimuovere gli standard](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Rimossa la tabella Microsoft.Security/securityStatuses da Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Valutazione delle vulnerabilità per computer locali e multicloud (anteprima)

Lo strumento di analisi integrato per la valutazione delle vulnerabilità di [Azure Defender per i server](defender-for-servers-introduction.md) (con tecnologia Qualys) ora analizza i server con abilitazione di Azure Arc.

Quando Azure Arc viene abilitato in computer non di Azure, il Centro sicurezza offre la possibilità di distribuirvi lo strumento integrato di analisi delle vulnerabilità, manualmente e su larga scala.

Con questo aggiornamento, è possibile sfruttare la potenza di **Azure Defender per i server** per consolidare il programma di gestione delle vulnerabilità in tutti gli asset di Azure e non di Azure.

Funzionalità principali:

- Monitoraggio dello stato di provisioning dello strumento di analisi per la valutazione delle vulnerabilità nei computer Azure Arc
- Provisioning dell'agente di valutazione delle vulnerabilità nei computer Azure Arc Windows e Linux non protetti (manualmente e su larga scala)
- Ricezione e analisi delle vulnerabilità rilevate dagli agenti distribuiti (manualmente e su larga scala)
- Esperienza unificata per macchine virtuali di Azure e computer Azure Arc

[Altre informazioni sulla distribuzione dello strumento di analisi delle vulnerabilità integrato nei computer ibridi](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Altre informazioni sui server con abilitazione di Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Aggiunta una raccomandazione per Firewall di Azure (anteprima)

È stata aggiunta una nuova raccomandazione per proteggere tutte le reti virtuali con Firewall di Azure.

La raccomandazione **È consigliabile che le reti virtuali siano protette da Firewall di Azure** consiglia di limitare l'accesso alle reti virtuali ed evitare potenziali minacce tramite Firewall di Azure.

Altre informazioni su [Firewall di Azure](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes - Aggiornata la raccomandazione con una correzione rapida

La raccomandazione **Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes** include ora un'opzione per la correzione rapida.

Per altre informazioni su questa e su tutte le altre raccomandazioni del Centro sicurezza, vedere [Raccomandazioni sulla sicurezza: una guida di riferimento](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="La raccomandazione Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes con l'opzione per la correzione rapida":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Il dashboard Conformità con le normative include ora un'opzione per rimuovere gli standard

Il dashboard Conformità con le normative del Centro sicurezza fornisce informazioni dettagliate sul comportamento di conformità in base a come vengono soddisfatti specifici controlli e requisiti.

Il dashboard include un set predefinito di standard normativi. Se uno o più di questi standard non sono pertinenti per la propria organizzazione, è ora possibile rimuoverli facilmente dall'interfaccia utente per una sottoscrizione. Gli standard possono essere rimossi solo a livello della *sottoscrizione*, non nell'ambito del gruppo di gestione.

Per altre informazioni, vedere [Rimozione di uno standard dal dashboard](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Rimossa la tabella Microsoft.Security/securityStatuses da Azure Resource Graph (ARG)

Azure Resource Graph è un servizio di Azure progettato per offrire un'esplorazione efficiente delle risorse con la possibilità di eseguire query su larga scala in un determinato set di sottoscrizioni in modo da poter controllare l'ambiente efficacemente. 

Per il Centro sicurezza di Azure, è possibile usare Azure Resource Graph e il [linguaggio di query Kusto](/azure/data-explorer/kusto/query/) per eseguire query su un'ampia gamma di dati relativi alla postura di sicurezza. Esempio:

- L'inventario degli asset utilizza Azure Resource Graph
- È stata documentata una query di esempio di Azure Resource Graph che illustra come [identificare gli account senza l'opzione Multi-Factor Authentication (MFA) abilitata](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Azure Resource Graph include tabelle di dati da usare nelle query.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer e le tabelle disponibili":::

> [!TIP]
> La documentazione di Azure Resource Graph descrive tutte le tabelle disponibili nella sezione [Tabella di Azure Resource Graph e riferimenti sui tipi di risorsa](../governance/resource-graph/reference/supported-tables-resources.md).

Con questo aggiornamento, la tabella **Microsoft.Security/securityStatuses** è stata rimossa. L'API securityStatuses è ancora disponibile.

La sostituzione dei dati può essere eseguita tramite la tabella Microsoft.Security/Assessments.

La principale differenza tra Microsoft.Security/securityStatuses e Microsoft.Security/Assessments è che mentre la prima mostra l'aggregazione delle valutazioni, la seconda contiene un singolo record per ognuna.

Ad esempio, Microsoft.Security/securityStatuses restituisce un risultato con una matrice di due policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Mentre Microsoft.Security/Assessments contiene un record per ognuna di queste valutazioni dei criteri, come segue:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Esempio di conversione di una query di Azure Resource Graph esistente usando securityStatuses per usare la tabella Assessments:**

Query che fa riferimento a SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Query di sostituzione per la tabella Assessments:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Per altre informazioni, vedere i collegamenti seguenti:
- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Linguaggio di query Kusto (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>Settembre 2020

Gli aggiornamenti del mese di settembre includono quanto segue:
- [Nuovo aspetto per il Centro sicurezza](#security-center-gets-a-new-look)
- [Rilascio di Azure Defender](#azure-defender-released)
- [Disponibilità generale di Azure Defender per Key Vault](#azure-defender-for-key-vault-is-generally-available)
- [Disponibilità generale della protezione di Azure Defender per Archiviazione per File e ADLS Gen2](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Disponibilità generale degli strumenti di inventario delle risorse](#asset-inventory-tools-are-now-generally-available)
- [Possibilità di disabilitare un risultato specifico della vulnerabilità per le analisi dei registri contenitori e delle macchine virtuali](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Esentare una risorsa da una raccomandazione](#exempt-a-resource-from-a-recommendation)
- [Esperienza per più cloud grazie ai connettori AWS e GCP nel Centro sicurezza](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Aggregazione di raccomandazioni sulla protezione dei carichi di lavoro Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Possibilità di esportazione continua dei risultati delle valutazioni delle vulnerabilità](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Possibilità di impedire errori di configurazione della sicurezza tramite l'applicazione di raccomandazioni durante la creazione di nuove risorse](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Miglioramento alle raccomandazioni per i gruppi di sicurezza di rete](#network-security-group-recommendations-improved)
- [Deprecazione della raccomandazione in anteprima "I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes" del servizio Azure Kubernetes](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Miglioramento delle notifiche tramite posta elettronica dal Centro sicurezza di Azure](#email-notifications-from-azure-security-center-improved)
- [Raccomandazioni in anteprima non incluse nel punteggio di sicurezza](#secure-score-doesnt-include-preview-recommendations)
- [Indicatore di gravità e intervallo di aggiornamento ora inclusi nelle raccomandazioni](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Nuovo aspetto per il Centro sicurezza

È stata rilasciata un'interfaccia utente aggiornata per le pagine del portale del Centro sicurezza. Le nuove pagine includono una nuova pagina di panoramica, oltre a dashboard per il punteggio di sicurezza, l'inventario delle risorse e Azure Defender.

La pagina di panoramica riprogettata include ora un riquadro per l'accesso al punteggio di sicurezza, all'inventario delle risorse e ai dashboard di Azure Defender. Include anche un riquadro di collegamento al dashboard di conformità alle normative.

Altre informazioni sulla [pagina di panoramica](overview-page.md).


### <a name="azure-defender-released"></a>Rilascio di Azure Defender

**Azure Defender** è la soluzione CWPP (Cloud Workload Protection Platform) integrata nel Centro sicurezza per offrire una protezione avanzata e intelligente dei carichi di lavoro di Azure e ibridi. Sostituisce l'opzione Standard del piano tariffario del Centro sicurezza. 

Quando si abilita Azure Defender dall'area **Prezzi e impostazioni** del Centro sicurezza di Azure, vengono abilitati contemporaneamente tutti i piani seguenti di Defender e vengono fornite difese complete per i livelli di calcolo, dati e servizio dell'ambiente:

- [Azure Defender per server](defender-for-servers-introduction.md)
- [Azure Defender per il servizio app](defender-for-app-service-introduction.md)
- [Azure Defender per Archiviazione](defender-for-storage-introduction.md)
- [Azure Defender per SQL](defender-for-sql-introduction.md)
- [Azure Defender per Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender per registri contenitori](defender-for-container-registries-introduction.md)

Ogni piano è illustrato separatamente nella documentazione del Centro sicurezza.

Grazie al dashboard dedicato, Azure Defender offre avvisi di sicurezza e protezione avanzata dalle minacce per macchine virtuali, database SQL, contenitori, applicazioni Web, rete e altro ancora.

[Altre informazioni su Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Disponibilità generale di Azure Defender per Key Vault

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

**Azure Defender per Key Vault** fornisce la protezione avanzata dalle minacce nativa di Azure per Azure Key Vault, offrendo un livello aggiuntivo di intelligence sulla sicurezza. Azure Defender per Key Vault protegge quindi molte delle risorse che dipendono dagli account di Key Vault.

Il piano facoltativo è ora disponibile a livello generale. Questa funzionalità era disponibile in anteprima come "Advanced Threat Protection per Azure Key Vault".

Le pagine di Key Vault nel portale di Azure includono ora inoltre una pagina **Sicurezza** dedicata per le raccomandazioni e gli avvisi del **Centro sicurezza**.

Per altre informazioni, vedere [Azure Defender per Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Disponibilità generale della protezione di Azure Defender per Archiviazione per File e ADLS Gen2 

**Azure Defender per Archiviazione** rileva le attività potenzialmente dannose negli account di archiviazione di Azure. I dati possono essere protetti indipendentemente dal fatto che siano archiviati come contenitori BLOB, condivisioni file o data lake.

È ora disponibile a livello generale il supporto per [File di Azure](../storage/files/storage-files-introduction.md) e [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

A partire dal 1° ottobre 2020 verranno applicati addebiti per la protezione delle risorse in questi servizi.

Per altre informazioni, vedere [Azure Defender per Archiviazione](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Disponibilità generale degli strumenti di inventario delle risorse

La pagina di inventario delle risorse del Centro sicurezza di Azure offre una pagina singola per visualizzare il comportamento di sicurezza delle risorse connesse al Centro sicurezza.

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza. Fornisce quindi raccomandazioni su come correggere tali vulnerabilità.

Le risorse a cui sono associate raccomandazioni in attesa verranno visualizzate nell'inventario.

Per altre informazioni, vedere [Esplorare e gestire le risorse con l'inventario degli asset](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Possibilità di disabilitare un risultato specifico della vulnerabilità per le analisi dei registri contenitori e delle macchine virtuali

Azure Defender include rilevatori di vulnerabilità per analizzare le immagini nel Registro Azure Container e nelle macchine virtuali.

Se l'organizzazione deve ignorare un risultato invece di correggerlo, è possibile disabilitarlo facoltativamente. I risultati disabilitati non influiscono sul punteggio di sicurezza e non generano elementi non significativi.

Quando un risultato corrisponde ai criteri definiti nelle regole di disabilitazione, non verrà visualizzato nell'elenco di risultati.

Questa opzione è disponibile nella pagina dei dettagli delle raccomandazioni per:

- **È consigliabile correggere le vulnerabilità nelle immagini del Registro Azure Container**
- **È consigliabile correggere le vulnerabilità nelle macchine virtuali**

Per altre informazioni, vedere [Disabilitare risultati specifici per le immagini del contenitore](defender-for-container-registries-usage.md#disable-specific-findings-preview) e [Disabilitare risultati specifici per le macchine virtuali](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Esentare una risorsa da una raccomandazione

Una risorsa verrà occasionalmente elencata come non integra rispetto a una raccomandazione specifica, con una conseguente riduzione del punteggio di sicurezza, anche se si ritiene che si tratti di un errore. È possibile che sia stata corretta da un processo non rilevato dal Centro sicurezza o che l'organizzazione abbia deciso di accettare il rischio per tale risorsa specifica. 

In questi casi è possibile creare una regola di esenzione e assicurare che la risorsa non sia elencata in futuro tra le risorse non integre. Queste regole possono includere giustificazioni documentate, come illustrato di seguito.

Per altre informazioni, vedere [Esentare una risorsa dalle raccomandazioni e dal punteggio di sicurezza](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Esperienza per più cloud grazie ai connettori AWS e GCP nel Centro sicurezza

I carichi di lavoro cloud si estendono in genere su più piattaforme cloud, quindi anche i servizi di sicurezza cloud devono adottare lo stesso approccio.

Il Centro sicurezza di Azure protegge ora i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

L'onboarding degli account di AWS e GCP nel Centro sicurezza consente di integrare AWS Security Hub, GCP Security Command e il Centro sicurezza di Azure. 

Per altre informazioni, vedere [Connettere gli account di AWS al Centro sicurezza di Azure](quickstart-onboard-aws.md) e [Connettere gli account di GCP al Centro sicurezza di Azure](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Aggregazione di raccomandazioni sulla protezione dei carichi di lavoro Kubernetes

Per assicurare che i carichi di lavoro di Kubernetes siano protetti per impostazione predefinita, il Centro sicurezza aggiunge raccomandazioni per la protezione avanzata a livello di Kubernetes, incluse opzioni di applicazione con il controllo ammissione di Kubernetes.

Dopo l'installazione del componente aggiuntivo di Criteri di Azure per Kubernetes nel cluster del servizio Azure Kubernetes, ogni richiesta al server API Kubernetes sarà monitorata rispetto al set predefinito di procedure consigliate prima del salvataggio permanente nel cluster. È quindi possibile configurare l'imposizione delle procedure consigliate e renderle obbligatorie per i carichi di lavoro futuri.

È ad esempio possibile imporre che i contenitori con privilegi non debbano essere creati ed eventuali richieste future di creazione di tali contenitori verranno bloccate.

Per altre informazioni, vedere [Procedure consigliate per la protezione dei carichi di lavoro con il controllo ammissione di Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Possibilità di esportazione continua dei risultati delle valutazioni delle vulnerabilità

È possibile usare l'esportazione continua per eseguire lo streaming di avvisi e raccomandazioni in tempo reale in Hub eventi di Azure, aree di lavoro Log Analytics o Monitoraggio di Azure. È quindi possibile integrare questi dati con soluzioni di informazioni di sicurezza e gestione degli eventi, tra cui Azure Sentinel, Power BI, Esplora dati di Azure e altre ancora.

Gli strumenti integrati di valutazione delle vulnerabilità del Centro sicurezza restituiscono risultati sulle risorse sotto forma di raccomandazioni di utilità pratica con una raccomandazione "padre", ad esempio "È consigliabile correggere le vulnerabilità nelle macchine virtuali". 

I risultati di sicurezza sono ora disponibili per l'esportazione tramite l'esportazione continua quando si selezionano le raccomandazioni e si abilita l'opzione **Includi i risultati per la sicurezza**.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Interruttore Includi i risultati per la sicurezza nella configurazione dell'esportazione continua" :::

Pagine correlate:

- [Soluzione integrata di valutazione delle vulnerabilità del Centro sicurezza per macchine virtuali di Azure](deploy-vulnerability-assessment-vm.md)
- [Soluzione integrata di valutazione delle vulnerabilità del Centro sicurezza per immagini del Registro Azure Container](defender-for-container-registries-usage.md)
- [Esportazione continua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Possibilità di impedire errori di configurazione della sicurezza tramite l'applicazione di raccomandazioni durante la creazione di nuove risorse

Gli errori di configurazione della sicurezza sono una delle cause principali degli eventi imprevisti di sicurezza. Il Centro sicurezza consente ora di contribuire a *evitare* gli errori di configurazione delle nuove risorse per quanto riguarda raccomandazioni specifiche. 

Questa funzionalità può contribuire alla protezione dei carichi di lavoro e alla stabilizzazione del punteggio di sicurezza.

È possibile imporre una configurazione di sicurezza, basata su una raccomandazione specifica, in due modi:

- L'effetto **Deny** di Criteri di Azure consente di interrompere la creazione di risorse non integre

- L'opzione **Imponi** consente di sfruttare i vantaggi dell'effetto **DeployIfNotExist** di Criteri di Azure e di correggere automaticamente eventuali risorse non conformi al momento della creazione
 
Questa opzione è disponibile per raccomandazioni di sicurezza selezionate e si trova nella parte superiore della pagina dei dettagli delle risorse.

Per altre informazioni, vedere [Impedire gli errori di configurazione con le raccomandazioni di tipo Imponi/Nega](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Miglioramento alle raccomandazioni per i gruppi di sicurezza di rete

Le raccomandazioni di sicurezza seguenti correlate ai gruppi di sicurezza di rete sono state migliorate per ridurre alcune istanze di falsi positivi.

- È consigliabile limitare tutte le porte di rete nei gruppi di sicurezza di rete associati alla VM
- È consigliabile chiudere le porte di gestione nelle macchine virtuali
- Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete
- Le subnet devono essere associate a un gruppo di sicurezza di rete


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Deprecazione della raccomandazione in anteprima "I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes" del servizio Azure Kubernetes

La raccomandazione in anteprima "I criteri di sicurezza pod devono essere definiti nei servizi Kubernetes" verrà deprecata come illustrato nella documentazione del [servizio Azure Kubernetes](../aks/use-pod-security-policies.md).

La funzionalità Criteri di sicurezza dei pod (anteprima) è configurata per la deprecazione e non sarà più disponibile dopo il 15 ottobre 2020. Verrà sostituita da Criteri di Azure per il servizio Azure Kubernetes.

Dopo la deprecazione di Criteri di sicurezza dei pod (anteprima), sarà necessario disabilitare la funzionalità in eventuali cluster esistenti che usano la funzionalità deprecata per eseguire aggiornamenti futuri del cluster e mantenere il supporto tecnico di Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Miglioramento delle notifiche tramite posta elettronica dal Centro sicurezza di Azure

Le aree seguenti dei messaggi di posta elettronica correlati agli avvisi di sicurezza sono state migliorate: 

- Aggiunta della possibilità di inviare notifiche tramite posta elettronica sugli avvisi per tutti i livelli di gravità
- Aggiunta la possibilità di inviare notifiche agli utenti con ruoli di Azure diversi nella sottoscrizione
- Notifiche proattive per i proprietari delle sottoscrizioni per impostazione predefinita in caso di avvisi a gravità alta, con probabilità elevata di essere violazioni effettive
- Rimozione del campo relativo al numero di telefono dalla pagina di configurazione delle notifiche tramite posta elettronica

Per altre informazioni, vedere [Configurare le notifiche tramite posta elettronica per gli avvisi di sicurezza](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Raccomandazioni in anteprima non incluse nel punteggio di sicurezza 

Il Centro sicurezza valuta continuamente risorse, sottoscrizioni e organizzazione per rilevare problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato.

Quando vengono individuate nuove minacce, vengono resi disponibili nuovi consigli sulla sicurezza nel Centro sicurezza tramite nuove raccomandazioni. Per evitare modifiche inaspettate al punteggio di sicurezza e per offrire un periodo di tolleranza in cui è possibile esplorare le nuove raccomandazioni prima che influiscano sui punteggi, le raccomandazioni contrassegnate come **Anteprima** non sono più incluse nei calcoli del punteggio di sicurezza. È comunque necessario correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima.

Le raccomandazioni di tipo **Anteprima** non contrassegnano inoltre una risorsa come "Non integra".

Esempio di una raccomandazione in anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Raccomandazione con il flag di anteprima":::

[Altre informazioni sul punteggio di sicurezza](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Indicatore di gravità e intervallo di aggiornamento ora inclusi nelle raccomandazioni

La pagina dei dettagli per le raccomandazioni include ora un indicatore dell'intervallo di aggiornamento, se rilevante, e un'indicazione chiara della gravità della raccomandazione.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Pagina delle raccomandazioni che mostra l'aggiornamento e la gravità":::



## <a name="august-2020"></a>Agosto 2020

Gli aggiornamenti del mese di agosto includono quanto segue:

- [Inventario delle risorse: nuova visualizzazione potente del comportamento di sicurezza delle risorse](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Aggiunta di supporto per le impostazioni predefinite per la sicurezza di Azure Active Directory per l'autenticazione a più fattori](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Aggiunta di una raccomandazione per le entità servizio](#service-principals-recommendation-added)
- [Valutazione delle vulnerabilità nelle VM: consolidamento di raccomandazioni e criteri](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Aggiunta di nuovi criteri di sicurezza del servizio Azure Kubernetes all'iniziativa ASC_default per l'uso esclusivo da parte dei clienti dell'anteprima privata](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventario delle risorse: nuova visualizzazione potente del comportamento di sicurezza delle risorse

L'inventario delle risorse del Centro sicurezza, attualmente disponibile in anteprima, offre una modalità per visualizzare il comportamento di sicurezza delle risorse connesse al Centro sicurezza.

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza. Fornisce quindi raccomandazioni su come correggere tali vulnerabilità. Le risorse a cui sono associate raccomandazioni in attesa verranno visualizzate nell'inventario.

È possibile usare la visualizzazione e i rispettivi filtri per esplorare i dati del comportamento di sicurezza ed eseguire altre azioni in base ai risultati.

Altre informazioni sull'[inventario delle risorse](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Aggiunta di supporto per le impostazioni predefinite per la sicurezza di Azure Active Directory per l'autenticazione a più fattori

Il Centro sicurezza ha aggiunto il supporto completo per le [impostazioni predefinite per la sicurezza](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), ovvero le protezione gratuite di Microsoft per la sicurezza delle identità.

Le impostazioni predefinite per la sicurezza offrono impostazioni preconfigurate per la sicurezza delle identità per proteggere l'organizzazione da attacchi comuni correlati alle identità. Le impostazioni predefinite per la sicurezza proteggono già più di 5 milioni di tenant complessivamente. 50.000 tenant sono protetti anche dal Centro sicurezza.

Il Centro sicurezza fornisce ora una raccomandazione sulla sicurezza ogni volta che identifica una sottoscrizione di Azure in cui non sono abilitate le impostazioni predefinite per la sicurezza. Fino a oggi il Centro sicurezza ha consigliato l'abilitazione dell'autenticazione a più fattori con l'accesso condizionale, incluso nella licenza di Azure Active Directory (AD) Premium. Per i clienti che usano Azure AD Gratuito è ora consigliabile abilitare le impostazioni predefinite per la sicurezza. 

L'obiettivo consiste nell'invitare più clienti a proteggere gli ambienti cloud tramite autenticazione a più fattori e attenuare uno dei rischi più elevati e con impatto più significativo sul [punteggio di sicurezza](secure-score-security-controls.md).

Altre informazioni sulle [impostazioni predefinite per la sicurezza](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Aggiunta di una raccomandazione per le entità servizio

È stata aggiunta una nuova raccomandazione per consigliare ai clienti del Centro sicurezza che usano i certificati di gestione per gestire le sottoscrizioni di passare alle entità servizio.

La raccomandazione **Per proteggere le sottoscrizioni è consigliabile usare le entità servizio invece dei certificati di gestione** consiglia di usare le entità servizio o Azure Resource Manager per migliorare la sicurezza della gestione delle sottoscrizioni. 

Altre informazioni su [Oggetti applicazione ed entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Valutazione delle vulnerabilità nelle VM: consolidamento di raccomandazioni e criteri

Il Centro sicurezza esamina le VM per rilevare se eseguono una soluzione di valutazione delle vulnerabilità. Se non viene trovata alcuna soluzione di valutazione delle vulnerabilità, il Centro sicurezza fornisce una raccomandazione per semplificare la distribuzione.

Quando vengono trovate vulnerabilità, il Centro sicurezza fornisce una raccomandazione che riepiloga i risultati per consentire l'indagine e la correzione, se necessario.

Per assicurare un'esperienza coerente per tutti gli utenti, indipendentemente dal tipo di rilevatore usato, quattro raccomandazioni sono state combinate nelle due raccomandazioni seguenti:

|Raccomandazione unificata|Descrizione delle modifiche|
|----|:----|
|**È consigliabile abilitare una soluzione di valutazione della vulnerabilità nelle macchine virtuali**|Sostituisce le due raccomandazioni seguenti:<br> **•** Abilitare la soluzione di valutazione delle vulnerabilità predefinita nelle macchine virtuali (con tecnologia Qualys) - Ora deprecata e inclusa nel livello Standard<br> **•** È consigliabile installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali - Ora deprecata (livelli Standard e Gratuito)|
|**È consigliabile correggere le vulnerabilità nelle macchine virtuali**|Sostituisce le due raccomandazioni seguenti:<br>**•** Correggere le vulnerabilità rilevate nelle macchine virtuali (con tecnologia Qualys) - Ora deprecata<br>**•** È consigliabile correggere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità - Ora deprecata|
|||

Sarà ora possibile usare la stessa raccomandazione per distribuire l'estensione di valutazione delle vulnerabilità del Centro sicurezza o una soluzione con licenza privata ("BYOL") da un partner quale Qualys o Rapid7.

Quando le vulnerabilità vengono trovate e segnalate al Centro sicurezza, una singola raccomandazione informerà gli utenti in merito alla disponibilità dei risultati, indipendentemente dalla soluzione di valutazione delle vulnerabilità che le ha individuate.

#### <a name="updating-dependencies"></a>Aggiornamento delle dipendenze

Se sono presenti script, query o automazioni che fanno riferimento a raccomandazioni o chiavi/nomi di criteri precedenti, usare la tabella seguente per aggiornare i riferimenti:

##### <a name="before-august-2020"></a>Prima del mese di agosto 2020

|Recommendation|Ambito|
|----|:----|
|**Abilitare la soluzione di valutazione delle vulnerabilità predefinita nelle macchine virtuali (con tecnologia Qualys)**<br>Chiave: 550e890b-e652-4d22-8274-60b3bdb24c63|Predefinito|
|**Correggere le vulnerabilità rilevate nelle macchine virtuali (con tecnologia Qualys)**<br>Chiave: 1195afff-c881-495e-9bc5-1486211ae03f|Predefinito|
|**È consigliabile installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali**<br>Chiave: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità**<br>Chiave: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Policy|Ambito|
|----|:----|
|**La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali**<br>ID criterio: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Predefinito|
|**È consigliabile correggere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità**<br>ID criterio: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Dal mese di agosto 2020

|Recommendation|Ambito|
|----|:----|
|**È consigliabile abilitare una soluzione di valutazione della vulnerabilità nelle macchine virtuali**<br>Chiave: ffff0522-1e88-47fc-8382-2a80ba848f5d|Predefinito + BYOL|
|**È consigliabile correggere le vulnerabilità nelle macchine virtuali**<br>Chiave: 1195afff-c881-495e-9bc5-1486211ae03f|Predefinito + BYOL|
||||

|Policy|Ambito|
|----|:----|
|[**La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID criterio: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Predefinito + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Aggiunta di nuovi criteri di sicurezza del servizio Azure Kubernetes all'iniziativa ASC_default per l'uso esclusivo da parte dei clienti dell'anteprima privata

Per assicurare che i carichi di lavoro di Kubernetes siano protetti per impostazione predefinita, il Centro sicurezza aggiunge criteri e raccomandazioni per la protezione avanzata a livello di Kubernetes, incluse opzioni di imposizione con il controllo ammissione di Kubernetes.

La fase iniziale di questo progetto include un'anteprima privata e l'aggiunta di nuovi criteri, disabilitati per impostazione predefinita, all'iniziativa ASC_default.

È possibile ignorare questi criteri, senza impatto sull'ambiente. Se si vogliono abilitare i criteri, iscriversi all'anteprima in https://aka.ms/SecurityPrP e selezionare una delle opzioni seguenti:

1. **Anteprima singola**: per partecipare solo a questa anteprima privata. Indicare esplicitamente "Analisi continua del Centro sicurezza di Azure" come anteprima a cui si vuole partecipare.
1. **Programma in corso**: per partecipare a questa anteprima privata e alle anteprime private future. Sarà necessario completare un profilo e accettare un contratto sulla privacy.