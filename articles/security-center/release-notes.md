---
title: Note sulla versione per il Centro sicurezza di Azure
description: Descrizione delle novità e delle modifiche apportate al Centro sicurezza di Azure
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/20/2021
ms.author: memildin
ms.openlocfilehash: 92138b0253d28c8bf2617ee7b17474f30ac5cafe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751204"
---
# <a name="whats-new-in-azure-security-center"></a>Novità del Centro sicurezza di Azure

Il Centro sicurezza è in fase di sviluppo attivo e riceve regolarmente miglioramenti. Per stare al passo con gli sviluppi più recenti, questa pagina fornisce informazioni su funzionalità nuove e deprecate, oltre che sulle correzioni dei bug.

Questa pagina viene aggiornata regolarmente, quindi è consigliabile consultarla spesso. 

Per informazioni sulle modifiche *pianificate* che saranno presto disponibili nel Centro sicurezza, vedere [Modifiche importanti che interesseranno il Centro sicurezza di Azure](upcoming-changes.md). 

> [!TIP]
> Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarle in [Archive for What's new in Azure Security Center](release-notes-archive.md) (Archivio per le novità nel Centro sicurezza di Azure).

## <a name="april-2021"></a>Aprile 2021

Gli aggiornamenti del mese di aprile includono quanto segue:
- [Le immagini del registro contenitori di cui è stato recentemente estratto vengono ora rieguite l'analisi settimanalmente (disponibilità generale)](#recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability)
- [Usare Azure Defender per Kubernetes per proteggere le distribuzioni ibride e multi-cloud di Kubernetes (anteprima)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [Raccomandazioni per abilitare Azure Defender dns e Resource Manager (anteprima)](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview)
- [Quattro nuove raccomandazioni relative alla configurazione guest (anteprima)](#four-new-recommendations-related-to-guest-configuration-preview)
- [Le raccomandazioni della CMK sono passate al controllo di sicurezza delle procedure consigliate](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 avvisi Azure Defender deprecati](#11-azure-defender-alerts-deprecated)
- [Due raccomandazioni del controllo di sicurezza "Applica aggiornamenti di sistema" sono state deprecate](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [Azure Defender per SQL nel computer rimosso dal dashboard Azure Defender dati](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)

### <a name="recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability"></a>Le immagini del registro contenitori di cui è stato recentemente estratto vengono ora rieguite l'analisi settimanalmente (disponibilità generale)

Azure Defender per i registri contenitori include uno scanner di vulnerabilità incorporato. Questo scanner analizza immediatamente qualsiasi immagine di cui si esegue il push nel registro e qualsiasi immagine estratta negli ultimi 30 giorni.

Ogni giorno vengono individuate nuove vulnerabilità. Con questo aggiornamento, le immagini del contenitore che sono state estrasse dai registri durante gli ultimi 30 giorni verranno nuovamente analisi **ogni** settimana. Ciò garantisce che le vulnerabilità appena individuate siano identificate nelle immagini.

L'analisi viene addebitata in base all'immagine, quindi non sono disponibili costi aggiuntivi per queste analisi.

Per altre informazioni su questo scanner, vedere Usare Azure Defender registri contenitori per [analizzare le immagini alla ricerca di vulnerabilità.](defender-for-container-registries-usage.md)


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>Usare Azure Defender per Kubernetes per proteggere le distribuzioni ibride e multi-cloud di Kubernetes (anteprima)

Azure Defender per Kubernetes sta espandendo le funzionalità di protezione dalle minacce per proteggere i cluster ovunque siano distribuiti. Questa funzionalità è stata abilitata grazie [all'integrazione con Azure Arc Kubernetes](../azure-arc/kubernetes/overview.md) abilitato e le nuove funzionalità [delle estensioni](../azure-arc/kubernetes/extensions.md). 

Dopo aver abilitato Azure Arc nei cluster kubernetes non Azure, una nuova raccomandazione di Centro sicurezza di Azure offre di distribuire l'estensione Azure Defender a questi cluster con pochi clic.

Usare la raccomandazione ( nei **cluster Kubernetes** abilitati per Azure Arc deve essere installata l'estensione di Azure Defender ) e l'estensione per proteggere i cluster Kubernetes distribuiti in altri provider di servizi cloud, anche se non nei servizi Kubernetes gestiti.

Questa integrazione tra Centro sicurezza di Azure, Azure Defender e Azure Arc Kubernetes abilitata offre:

- Provisioning semplice dell'estensione Azure Defender a cluster Kubernetes Azure Arc non protetti (manualmente e su larga scala)
- Monitoraggio dell'estensione Azure Defender e del relativo stato di provisioning dal portale Azure Arc
- Le raccomandazioni sulla sicurezza del Centro sicurezza vengono segnalate nella nuova pagina Sicurezza del portale Azure Arc sicurezza
- Le minacce alla sicurezza identificate Azure Defender vengono segnalate nella nuova pagina Sicurezza del portale Azure Arc
- Azure Arc cluster Kubernetes abilitati sono integrati nella piattaforma Centro sicurezza di Azure e nell'esperienza

Per altre [informazioni, vedere Usare Azure Defender per Kubernetes cluster Kubernetes locali e multi-cloud.](defender-for-kubernetes-azure-arc.md)

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Centro sicurezza di Azure per distribuire l'estensione Azure Defender per Azure Arc cluster Kubernetes abilitati." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-preview"></a>Raccomandazioni per abilitare Azure Defender dns e Resource Manager (anteprima)

Sono state aggiunte due nuove raccomandazioni per semplificare il processo di [abilitazione](defender-for-resource-manager-introduction.md) Azure Defender per Resource Manager e [Azure Defender per DNS:](defender-for-dns-introduction.md)

- **Azure Defender per Resource Manager** deve essere abilitato: Defender per Resource Manager monitora automaticamente le operazioni di gestione delle risorse nell'organizzazione. Azure Defender rileva le minacce e avvisa l'utente in caso di attività sospette.
- **Azure Defender per DNS** deve essere abilitato: Defender per DNS offre un livello aggiuntivo di protezione per le risorse cloud monitorando continuamente tutte le query DNS dalle risorse di Azure. Azure Defender avvisi relativi ad attività sospette a livello DNS.

L'abilitazione Azure Defender piani comporta addebiti. Per informazioni sui dettagli sui prezzi per area, vedere la pagina dei prezzi del Centro sicurezza: https://aka.ms/pricing-security-center .

> [!TIP]
> Le raccomandazioni in anteprima non contrassegnano una risorsa come non integra e non sono incluse nei calcoli del punteggio di sicurezza. Correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima. Per altre informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Quattro nuove raccomandazioni relative alla configurazione guest (anteprima)

L'estensione [Configurazione guest di](../governance/policy/concepts/guest-configuration.md) Azure segnala al Centro sicurezza per garantire la protezione avanzata delle impostazioni in guest delle macchine virtuali. L'estensione non è necessaria per i server abilitati per Arc perché è inclusa nell'agente Arc Connected Machine. L'estensione richiede un'identità gestita dal sistema nel computer.

Sono state aggiunte quattro nuove raccomandazioni al Centro sicurezza per ottenere il massimo da questa estensione.

- Due raccomandazioni chiede di installare l'estensione e la relativa identità gestita dal sistema richiesta:
    - **L'estensione Configurazione guest deve essere installata nei computer**
    - **L'estensione Configurazione guest delle macchine virtuali deve essere distribuita con l'identità gestita assegnata dal sistema**

- Quando l'estensione è installata e in esecuzione, inizierà a controllare i computer e verrà richiesto di eseguire la protezione avanzata delle impostazioni, ad esempio la configurazione del sistema operativo e delle impostazioni dell'ambiente. Queste due raccomandazioni richiederanno di eseguire la protezione avanzata dei computer Windows e Linux come descritto:
    - **Windows Defender Exploit Guard deve essere abilitato nei computer**
    - **L'autenticazione nei computer Linux deve richiedere chiavi SSH**

Per altre [informazioni, Criteri di Azure della configurazione guest di .](../governance/policy/concepts/guest-configuration.md)

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>Le raccomandazioni della CMK sono passate al controllo di sicurezza delle procedure consigliate

Il programma di sicurezza di ogni organizzazione include requisiti di crittografia dei dati. Per impostazione predefinita, i dati dei clienti di Azure vengono crittografati quando sono in pausa con chiavi gestite dal servizio. Tuttavia, le chiavi gestite dal cliente (CMK) sono comunemente necessarie per soddisfare gli standard di conformità alle normative. I CK consentono di crittografare i dati [con Azure Key Vault](../key-vault/general/overview.md) chiave creata e di proprietà dell'utente. In questo modo è possibile avere il controllo completo e la responsabilità del ciclo di vita chiave, incluse la rotazione e la gestione.

Centro sicurezza di Azure di sicurezza sono gruppi logici di raccomandazioni di sicurezza correlate e riflettono le superfici di attacco vulnerabili. Ogni controllo ha un numero massimo di punti che è possibile aggiungere al punteggio di sicurezza se si corredono tutte le raccomandazioni elencate nel controllo, per tutte le risorse. Il **controllo di sicurezza Implementa procedure consigliate per** la sicurezza vale zero punti. Pertanto, le raccomandazioni in questo controllo non influiscono sul punteggio di sicurezza.

Le raccomandazioni elencate di seguito vengono spostate nel controllo di sicurezza Implementare le **procedure consigliate** per la sicurezza per riflettere meglio la natura facoltativa. Questo spostamento garantisce che queste raccomandazioni siano nel controllo più appropriato per soddisfare l'obiettivo.

- Gli account Azure Cosmos DB devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi
- Le aree di lavoro di Azure Machine Learning devono essere crittografate con una chiave gestita dal cliente
- Gli account di Servizi cognitivi devono abilitare la crittografia dei dati con una chiave gestita dal cliente (CMK)
- I registri contenitori devono essere crittografati con una chiave gestita dal cliente
- Le istanze gestite di SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi
- I server SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi
- Gli account di archiviazione devono usare la chiave gestita dal cliente per la crittografia

Per informazioni sulle raccomandazioni disponibili in ogni controllo di sicurezza, vedere [Controlli di sicurezza e relative raccomandazioni](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="11-azure-defender-alerts-deprecated"></a>11 avvisi Azure Defender deprecati

Gli 11 Azure Defender elencati di seguito sono stati deprecati.

- I nuovi avvisi sostituiranno questi due avvisi e forniranno una copertura migliore:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | ANTEPRIMA - Rilevata esecuzione della funzione "Get-AzureDomainInfo" del toolkit MicroBurst |
    | ARM_MicroBurstRunbook    | ANTEPRIMA - Rilevata esecuzione della funzione "Get-AzurePasswords" del toolkit MicroBurst  |
    |                          |                                                                          |

- Questi nove avvisi sono correlati a un connettore Azure Active Directory Identity Protection (IPC) che è già stato deprecato:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | Non familiareRilevazione  | Proprietà di accesso insolite |
    | AnonymousLogin      | Indirizzo IP anonimo          |
    | InfectedDeviceLogin | Indirizzo IP collegato a malware     |
    | ImpossibleTravel    | Spostamento fisico atipico               |
    | MaliciousIP         | Indirizzo IP dannoso          |
    | LeakedCredentials   | Credenziali perse            |
    | PasswordSpray       | Password Spray                |
    | LeakedCredentials   | Intelligence per le minacce di Azure AD  |
    | AADAI               | Intelligenza artificiale di Azure AD                   |
    |                     |                               |
 
    > [!TIP]
    > Questi nove avvisi IPC non sono mai stati avvisi del Centro sicurezza. Fanno parte del connettore Azure Active Directory (AAD) Identity Protection (IPC) che li stava inviando al Centro sicurezza. Negli ultimi due anni, gli unici clienti che hanno visualizzato tali avvisi sono le organizzazioni che hanno configurato l'esportazione (dal connettore al Certificato del servizio di sicurezza) nel 2019 o in una versione precedente. IPC di AAD ha continuato a mostrarli nei propri sistemi di avvisi e continua a essere disponibile in Azure Sentinel. L'unica modifica è che non vengono più visualizzate nel Centro sicurezza.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Due raccomandazioni del controllo di sicurezza "Applica aggiornamenti di sistema" sono state deprecate 

Le due raccomandazioni seguenti sono state deprecate e le modifiche potrebbero comportare un leggero impatto sul punteggio di sicurezza:

- **È consigliabile riavviare i computer per applicare gli aggiornamenti del sistema**
- **L'agente di monitoraggio deve essere installato nei computer**. Questa raccomandazione riguarda solo i computer locali e parte della logica verrà trasferita a un'altra raccomandazione. I problemi di integrità dell'agente di Log Analytics devono essere **risolti nei computer**

È consigliabile controllare le configurazioni di esportazione continua e di automazione del flusso di lavoro per verificare se queste raccomandazioni sono incluse. Inoltre, tutti i dashboard o altri strumenti di monitoraggio che potrebbero usarli devono essere aggiornati di conseguenza.

Per altre informazioni su queste raccomandazioni, vedere la pagina [di riferimento sulle raccomandazioni per la sicurezza](recommendations-reference.md).

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>Azure Defender per SQL nel riquadro del computer rimosso dal dashboard Azure Defender dati

L Azure Defender'area di copertura del dashboard include riquadri per i Azure Defender pertinenti per l'ambiente. A causa di un problema con la segnalazione del numero di risorse protette e non protette, è stato deciso di rimuovere temporaneamente lo stato di copertura delle risorse per **Azure Defender per SQL** nei computer fino a quando il problema non viene risolto.


## <a name="march-2021"></a>Marzo 2021

Gli aggiornamenti di marzo includono:

- [Firewall di Azure gestione integrata nel Centro sicurezza](#azure-firewall-management-integrated-into-security-center)
- [La valutazione della vulnerabilità SQL include ora l'esperienza "Disabilita regola" (anteprima)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Monitoraggio di Azure cartelle di lavoro integrate nel Centro sicurezza e tre modelli forniti](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Il dashboard di conformità alle normative include ora i report di controllo di Azure (anteprima)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [I dati delle raccomandazioni possono essere Azure Resource Graph con "Esplora in ARG"](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Aggiornamenti ai criteri per la distribuzione dell'automazione del flusso di lavoro](#updates-to-the-policies-for-deploying-workflow-automation)
- [Due raccomandazioni legacy non scrivono più i dati direttamente nel log attività di Azure](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Miglioramenti della pagina Raccomandazioni](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Firewall di Azure gestione integrata nel Centro sicurezza

Quando si apre il Centro sicurezza di Azure, la prima pagina che viene visualizzata è quella di panoramica. 

Questo dashboard interattivo offre una visualizzazione unificata del percorso di sicurezza dei carichi di lavoro cloud ibridi. Mostra inoltre avvisi di sicurezza, informazioni sulla copertura e altri dettagli.

Come parte dell'assistenza per visualizzare lo stato di sicurezza da un'esperienza centrale, l'Gestione firewall di Azure è stata integrata in questo dashboard. È ora possibile controllare lo stato di copertura del firewall in tutte le reti e gestire centralmente Firewall di Azure criteri a partire dal Centro sicurezza.

Altre informazioni su questo dashboard [sono Centro sicurezza di Azure pagina di panoramica di .](overview-page.md)

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Dashboard panoramica del Centro sicurezza con un riquadro per Firewall di Azure":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>La valutazione della vulnerabilità di SQL include ora l'esperienza "Disabilita regola" (anteprima)

Il Centro sicurezza include uno scanner di vulnerabilità predefinito che consente di individuare, monitorare e correggere potenziali vulnerabilità del database. I risultati delle analisi di valutazione offrono una panoramica dello stato di sicurezza dei computer SQL e informazioni dettagliate sui risultati della sicurezza.

Se l'organizzazione deve ignorare un risultato invece di correggerlo, è possibile disabilitarlo facoltativamente. I risultati disabilitati non influiscono sul punteggio di sicurezza e non generano elementi non significativi.

Per altre informazioni, [vedere Disabilitare risultati specifici.](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Monitoraggio di Azure cartelle di lavoro integrate nel Centro sicurezza e tre modelli forniti

Come parte di Ignite Spring 2021, è stata annunciata un'esperienza integrata Monitoraggio di Azure Workbooks nel Centro sicurezza.

È possibile sfruttare la nuova integrazione per iniziare a usare i modelli predefiniti dalla raccolta del Centro sicurezza. Usando i modelli di cartella di lavoro, è possibile accedere e creare report dinamici e visivi per tenere traccia del comportamento di sicurezza dell'organizzazione. È anche possibile creare nuove cartelle di lavoro basate sui dati del Centro sicurezza o su qualsiasi altro tipo di dati supportato e distribuire rapidamente le cartelle di lavoro della community dalla community GitHub del Centro sicurezza.

Sono disponibili tre report di modelli:

- **Punteggio di sicurezza nel tempo:** tenere traccia dei punteggi delle sottoscrizioni e delle modifiche alle raccomandazioni per le risorse
- **Aggiornamenti del sistema-** Visualizzare gli aggiornamenti di sistema mancanti in base a risorse, sistema operativo, gravità e altro ancora
- **Risultati della valutazione della** vulnerabilità- Visualizzare i risultati delle analisi delle vulnerabilità delle risorse di Azure

Informazioni sull'uso di questi report o sulla creazione di report [interattivi e dettagliati sui dati del Centro sicurezza.](custom-dashboards-azure-workbooks.md)

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Report Punteggio di sicurezza nel tempo":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Il dashboard di conformità alle normative include ora i report di controllo di Azure (anteprima)

Dalla barra degli strumenti del dashboard di conformità alle normative è ora possibile scaricare i report di certificazione di Azure e Dynamics. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Barra degli strumenti del dashboard conformità alle normative":::

È possibile selezionare la scheda per i tipi di report pertinenti (PCI, SOC, ISO e altri) e usare i filtri per trovare i report specifici necessari.

Altre informazioni sulla [gestione degli standard nel dashboard di conformità alle normative.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtro dell'elenco dei report di controllo di Azure disponibili":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>I dati delle raccomandazioni possono essere Azure Resource Graph con "Esplora in ARG"

Le pagine dei dettagli dei consigli includono ora il pulsante della barra degli strumenti "Esplora in ARG". Usare questo pulsante per aprire una Azure Resource Graph query ed esplorare, esportare e condividere i dati della raccomandazione.

Azure Resource Graph (ARG) consente l'accesso immediato alle informazioni sulle risorse negli ambienti cloud con funzionalità di filtro, raggruppamento e ordinamento affidabili. Si tratta di un modo rapido ed efficiente di eseguire query sulle informazioni nelle sottoscrizioni di Azure a livello di codice o dall'interno del portale di Azure.

Altre informazioni su [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Esplorare i dati di raccomandazione Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Aggiornamenti ai criteri per la distribuzione dell'automazione del flusso di lavoro

L'automazione dei processi di monitoraggio e risposta agli eventi imprevisti dell'organizzazione può migliorare significativamente il tempo necessario per indagare e attenuare gli eventi imprevisti relativi alla sicurezza.

Vengono forniti tre criteri Criteri di Azure 'DeployIfNotExist' che creano e configurano le procedure di automazione del flusso di lavoro in modo da poter distribuire le automazioni all'interno dell'organizzazione:

|Obiettivo  |Policy  |ID condizione  |
|---------|---------|---------|
|Automazione dei flussi di lavoro per gli avvisi di sicurezza|[Distribuisci automazione del flusso di lavoro per gli avvisi del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automazione dei flussi di lavoro per le raccomandazioni sulla sicurezza|[Distribuisci automazione del flusso di lavoro per le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automazione del flusso di lavoro per le modifiche di conformità alle normative|[Distribuire Automazione flusso di lavoro per Centro sicurezza di Azure conformità alle normative](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Sono disponibili due aggiornamenti per le funzionalità di questi criteri:

- Quando vengono assegnati, rimarranno abilitati dall'applicazione.
- È ora possibile personalizzare questi criteri e aggiornare i parametri anche dopo che sono già stati distribuiti. Ad esempio, se un utente vuole aggiungere un'altra chiave di valutazione o modificare una chiave di valutazione esistente, può farlo.

Introduzione ai [modelli di automazione dei flussi di lavoro](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Altre informazioni su come [automatizzare le risposte ai trigger](workflow-automation.md)del Centro sicurezza .


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Due raccomandazioni legacy non scrivono più i dati direttamente nel log attività di Azure 

Il Centro sicurezza passa i dati per quasi tutte le raccomandazioni di sicurezza Azure Advisor che, a sua volta, li scrive nel log [attività di Azure.](../azure-monitor/essentials/activity-log.md)

Per due raccomandazioni, i dati vengono scritti contemporaneamente direttamente nel log attività di Azure. Con questa modifica, il Centro sicurezza smette di scrivere i dati per queste raccomandazioni di sicurezza legacy direttamente nel log attività. Al contrario, i dati vengono esportati in Azure Advisor come per tutte le altre raccomandazioni.

Le due raccomandazioni legacy sono:
- È consigliabile risolvere i problemi di integrità di Endpoint Protection nei computer
- Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte

Se si accede alle informazioni per queste due raccomandazioni nella categoria "Raccomandazione di tipo TaskDiscovery" del log attività, questa opzione non è più disponibile.


### <a name="recommendations-page-enhancements"></a>Miglioramenti della pagina Raccomandazioni 

È stata rilasciata una versione migliorata dell'elenco di raccomandazioni per presentare più informazioni a colpo d'occhio.

Nella pagina verrà visualizzato:

1. Punteggio massimo e punteggio corrente per ogni controllo di sicurezza.
1. Icone che sostituiscono tag come **Correzione rapida e** **Anteprima.**
1. Nuova colonna che mostra [l'iniziativa Policy correlata](security-policy-concept.md) a ogni raccomandazione, visibile quando l'opzione "Raggruppa per controlli" è disabilitata.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Miglioramenti alla pagina Centro sicurezza di Azure raccomandazioni - Marzo 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Miglioramenti all'Centro sicurezza di Azure'elenco di raccomandazioni &quot;flat&quot; di marzo 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Per altre informazioni, vedere [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).


## <a name="february-2021"></a>Febbraio 2021

Gli aggiornamenti di febbraio includono:

- [Nuova pagina degli avvisi di sicurezza nel portale di Azure disponibile a livello generale](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Raccomandazioni sulla protezione dei carichi di lavoro di Kubernetes rilasciate per la disponibilità generale](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [L'integrazione di Microsoft Defender per endpoint con Azure Defender ora supporta Windows Server 2019 e Windows 10 Virtual Desktop (WVD) (in anteprima)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Collegamento diretto ai criteri dalla pagina dei dettagli delle raccomandazioni](#direct-link-to-policy-from-recommendation-details-page)
- [La raccomandazione sulla classificazione dei dati SQL non influisce più sul punteggio di sicurezza](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Le automazioni del flusso di lavoro possono essere attivate dalle modifiche alle valutazioni della conformità alle normative (in anteprima)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Miglioramenti apportati alla pagina dell'inventario asset](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nuova pagina degli avvisi di sicurezza nella portale di Azure disponibile a livello generale

La pagina Avvisi di sicurezza del Centro sicurezza di Azure è stata riprogettata per offrire:

- **Esperienza di analisi migliorata per** gli avvisi: per ridurre l'affaticamento degli avvisi e concentrarsi sulle minacce più rilevanti, l'elenco include filtri personalizzabili e opzioni di raggruppamento.
- **Altre informazioni nell'elenco degli avvisi,** ad esempio MITRE ATT&tattiche ACK.
- **Pulsante per creare avvisi di esempio:** per valutare Azure Defender funzionalità e testare gli avvisi. configurazione (per l'integrazione SIEM, le notifiche di posta elettronica e le automazioni del flusso di lavoro), è possibile creare avvisi di esempio da tutti Azure Defender piani.
- **Allineamento con l'esperienza** degli eventi imprevisti di Azure Sentinel: per i clienti che usano entrambi i prodotti, il passaggio da un prodotto all'altro è ora un'esperienza più semplice ed è facile imparare uno dall'altro.
- **Prestazioni migliori per** elenchi di avvisi di grandi dimensioni.
- **Navigazione tramite tastiera** nell'elenco di avvisi.
- **Avvisi di Azure Resource Graph**: è possibile eseguire query sugli avvisi in Azure Resource Graph, l'API di tipo Kusto per tutte le risorse. Questa funzionalità è utile anche per creare dashboard di avvisi personalizzati. Vedere [altre informazioni su Azure Resource Graph](../governance/resource-graph/index.yml).
- **Creare una funzionalità di avvisi di esempio:** per creare avvisi di esempio dalla nuova esperienza di avvisi, vedere Generare avvisi Azure Defender [di esempio](security-center-alert-validation.md#generate-sample-azure-defender-alerts).

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Centro sicurezza di Azure degli avvisi di sicurezza di Centro sicurezza di Azure":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Raccomandazioni sulla protezione dei carichi di lavoro di Kubernetes rilasciate per disponibilità generale

Siamo lieti di annunciare la disponibilità generale (GA) del set di raccomandazioni per le protezioni del carico di lavoro Kubernetes.

Per garantire la sicurezza dei carichi di lavoro kubernetes per impostazione predefinita, il Centro sicurezza ha aggiunto raccomandazioni per la protezione avanzata a livello di Kubernetes, incluse le opzioni di imposizione con il controllo di ammissione kubernetes.

Quando il componente aggiuntivo Criteri di Azure per Kubernetes viene installato nel cluster servizio Azure Kubernetes (AKS), ogni richiesta al server API Kubernetes verrà monitorata in base al set predefinito di procedure consigliate, visualizzate come 13 raccomandazioni di sicurezza, prima di essere rese persistenti nel cluster. È quindi possibile configurare l'imposizione delle procedure consigliate e renderle obbligatorie per i carichi di lavoro futuri.

È ad esempio possibile imporre che i contenitori con privilegi non debbano essere creati ed eventuali richieste future di creazione di tali contenitori verranno bloccate.

Per altre informazioni, vedere [Procedure consigliate per la protezione dei carichi di lavoro con il controllo ammissione di Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Anche se le raccomandazioni erano in anteprima, non hanno eseguito il rendering non integro di una risorsa cluster del servizio Servizio Web Diaks e non sono state incluse nei calcoli del punteggio di sicurezza. con questo annuncio GA, questi verranno inclusi nel calcolo del punteggio. Se non sono già stati corretti, ciò potrebbe comportare un leggero impatto sul punteggio di sicurezza. È possibile correggere questi problemi laddove possibile, come descritto in [Correggere le raccomandazioni in Centro sicurezza di Azure](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>L'integrazione di Microsoft Defender per endpoint con Azure Defender ora supporta Windows Server 2019 e Windows 10 Virtual Desktop (WVD) (in anteprima)

Microsoft Defender per endpoint è una soluzione olistica di sicurezza degli endpoint con distribuzione cloud. Fornisce la gestione e la valutazione delle vulnerabilità basate sul rischio, nonché il rilevamento e la risposta degli endpoint (EDR). Per un elenco completo dei vantaggi dell'uso di Defender per Endpoint con Centro sicurezza di Azure, vedere Proteggere gli endpoint con la soluzione EDR integrata del Centro [sicurezza: Microsoft Defender per endpoint.](security-center-wdatp.md)

Quando si abilita Azure Defender per i server in un server Windows, nel piano è inclusa una licenza per Defender per Endpoint. Se è già stato abilitato Azure Defender per i server e nella sottoscrizione sono presenti server Windows 2019, questi riceveranno automaticamente Defender per l'endpoint con questo aggiornamento. Non è necessaria alcuna azione manuale. 

Il supporto è stato ora ampliato per includere Windows Server 2019 e Desktop virtuale [Windows ( WVD).](../virtual-desktop/overview.md)

> [!NOTE]
> Se si abilita Defender per l'endpoint in un computer Windows Server 2019, assicurarsi che soddisfi i prerequisiti descritti in Abilitare [Microsoft Defender per l'integrazione degli endpoint.](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Collegamento diretto ai criteri dalla pagina dei dettagli delle raccomandazioni

Quando si esaminano i dettagli di una raccomandazione, è spesso utile essere in grado di visualizzare i criteri sottostanti. Per ogni raccomandazione supportata da un criterio, è presente un nuovo collegamento dalla pagina dei dettagli della raccomandazione:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Collegamento alla Criteri di Azure per i criteri specifici che supportano una raccomandazione":::

Usare questo collegamento per visualizzare la definizione dei criteri ed esaminare la logica di valutazione. 

Se si sta esaminando l'elenco di raccomandazioni nella guida di riferimento sulle raccomandazioni per la [sicurezza,](recommendations-reference.md)verranno visualizzati anche i collegamenti alle pagine di definizione dei criteri:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Accesso alla pagina Criteri di Azure per un criterio specifico direttamente dalla pagina di riferimento Centro sicurezza di Azure raccomandazioni" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>La raccomandazione sulla classificazione dei dati SQL non influisce più sul punteggio di sicurezza
La **raccomandazione I dati sensibili nei database SQL devono essere classificati** non influiscono più sul punteggio di sicurezza. Questa è l'unica raccomandazione nel controllo **di** sicurezza Applica classificazione dati, in modo che il controllo abbia ora un valore di punteggio sicuro pari a 0.

Per un elenco completo di tutti i controlli di sicurezza nel Centro sicurezza, insieme ai relativi punteggi e a un elenco delle raccomandazioni in ognuno, vedere Controlli di sicurezza [e relativi consigli.](secure-score-security-controls.md#security-controls-and-their-recommendations)

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Le automazioni del flusso di lavoro possono essere attivate dalle modifiche alle valutazioni della conformità alle normative (in anteprima)
È stato aggiunto un terzo tipo di dati alle opzioni di trigger per le automazioni del flusso di lavoro: modifiche alle valutazioni della conformità alle normative.

Informazioni su come usare gli strumenti di automazione del flusso di lavoro in [Automatizzare le risposte ai trigger del Centro sicurezza.](workflow-automation.md)

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Uso delle modifiche alle valutazioni della conformità alle normative per attivare un'automazione del flusso di lavoro" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Miglioramenti della pagina dell'inventario asset
La pagina inventario asset del Centro sicurezza è stata migliorata nei modi seguenti:

- I riepiloghi nella parte superiore della pagina includono **ora** sottoscrizioni non registrate, che mostrano il numero di sottoscrizioni senza centro sicurezza abilitato.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Conteggio delle sottoscrizioni non registrate nei riepiloghi nella parte superiore della pagina inventario asset":::

- I filtri sono stati espansi e migliorati per includere:
    - **Conteggi:** ogni filtro presenta il numero di risorse che soddisfano i criteri di ogni categoria

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Conteggi nei filtri nella pagina inventario asset di Centro sicurezza di Azure":::

    - **Contiene il filtro delle esenzioni** (facoltativo): restringere i risultati alle risorse che hanno o non hanno ottenuto esenzioni. Questo filtro non viene visualizzato per impostazione predefinita, ma è accessibile dal **pulsante Aggiungi** filtro.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Aggiunta del filtro &quot;contiene l'esenzione&quot; nella Centro sicurezza di Azure inventario degli asset del sito":::

Altre informazioni su come esplorare [e gestire le risorse con l'inventario degli asset.](asset-inventory.md)

## <a name="january-2021"></a>Gennaio 2021

Gli aggiornamenti di gennaio includono:

- [Azure Security Benchmark è ora l'iniziativa dei criteri predefinita per Centro sicurezza di Azure](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [La valutazione della vulnerabilità per computer locali e multi-cloud viene rilasciata per la disponibilità generale (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Il punteggio di sicurezza per i gruppi di gestione è ora disponibile in anteprima](#secure-score-for-management-groups-is-now-available-in-preview)
- [L'API Secure Score viene rilasciata per la disponibilità generale (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Protezione DNS inevasa aggiunta a Azure Defender per il servizio app](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [I connettori multi-cloud vengono rilasciati per la disponibilità generale (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Esentare intere raccomandazioni dal punteggio di sicurezza per le sottoscrizioni e i gruppi di gestione](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Gli utenti possono ora richiedere visibilità a livello di tenant dall'amministratore globale](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Aggiunte 35 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Esportazione in CSV dell'elenco filtrato di raccomandazioni](#csv-export-of-filtered-list-of-recommendations)
- [Le risorse "Non applicabili" ora segnalate come "Conformi" nelle Criteri di Azure valutazione](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Esportare snapshot settimanali dei dati relativi al punteggio di sicurezza e alla conformità alle normative con l'esportazione continua (anteprima)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Benchmark è ora l'iniziativa dei criteri predefinita per Centro sicurezza di Azure

Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. Questo benchmark ampiamente rispettato si basa sui controlli del [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e del National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.

Negli ultimi mesi, l'elenco di raccomandazioni sulla sicurezza incorporate del Centro sicurezza è aumentato significativamente per espandere la copertura di questo benchmark.

Da questa versione, il benchmark è alla base delle raccomandazioni del Centro sicurezza e completamente integrato come iniziativa dei criteri predefinita. 

Tutti i servizi di Azure hanno una pagina della baseline di sicurezza nella documentazione. Ad esempio, [si tratta della linea di base del Centro sicurezza.](security-baseline.md) Queste linee di base si basano su Azure Security Benchmark.

Se si usa il dashboard di conformità alle normative del Centro sicurezza, verranno visualizzati due istanze del benchmark durante un periodo di transizione:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Centro sicurezza di Azure dashboard di conformità alle normative che mostra Azure Security Benchmark":::

Le raccomandazioni esistenti non sono interessate e man mano che il benchmark aumenta, le modifiche verranno riflesse automaticamente all'interno del Centro sicurezza. 

Per altre informazioni, vedere le pagine seguenti:

- [Altre informazioni su Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Personalizzare il set di standard nel dashboard di conformità alle normative](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>La valutazione della vulnerabilità per i computer locali e multi-cloud viene rilasciata per la disponibilità generale

A ottobre è stata annunciata un'anteprima per l'analisi dei server con abilitazione di Azure Arc con lo strumento di analisi integrato per la valutazione delle vulnerabilità di [Azure Defender per i server](defender-for-servers-introduction.md) (con tecnologia Qualys).

È ora disponibile per la disponibilità generale.

Quando Azure Arc viene abilitato in computer non di Azure, il Centro sicurezza offre la possibilità di distribuirvi lo strumento integrato di analisi delle vulnerabilità, manualmente e su larga scala.

Con questo aggiornamento, è possibile sfruttare la potenza di **Azure Defender per i server** per consolidare il programma di gestione delle vulnerabilità in tutti gli asset di Azure e non di Azure.

Funzionalità principali:

- Monitoraggio dello stato di provisioning dello strumento di analisi per la valutazione delle vulnerabilità nei computer Azure Arc
- Provisioning dell'agente di valutazione delle vulnerabilità nei computer Azure Arc Windows e Linux non protetti (manualmente e su larga scala)
- Ricezione e analisi delle vulnerabilità rilevate dagli agenti distribuiti (manualmente e su larga scala)
- Esperienza unificata per macchine virtuali di Azure e computer Azure Arc

[Altre informazioni sulla distribuzione dello strumento di analisi delle vulnerabilità integrato nei computer ibridi](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Altre informazioni sui server con abilitazione di Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Il punteggio di sicurezza per i gruppi di gestione è ora disponibile in anteprima

La pagina punteggio di sicurezza mostra ora i punteggi di sicurezza aggregati per i gruppi di gestione oltre al livello di sottoscrizione. È quindi possibile visualizzare l'elenco dei gruppi di gestione nell'organizzazione e il punteggio per ogni gruppo di gestione.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Visualizzazione dei punteggi di sicurezza per i gruppi di gestione.":::

Altre informazioni sul [punteggio di sicurezza e i controlli di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>L'API punteggio di sicurezza viene rilasciata per la disponibilità generale (GA)

È ora possibile accedere al punteggio tramite [l'API di punteggio sicuro](/rest/api/securitycenter/securescores/). I metodi dell'API offrono la flessibilità necessaria per eseguire query nei dati e creare un meccanismo personalizzato per la creazione di report sui punteggi di sicurezza nel tempo. Ad esempio:

- usare **l'API Punteggi** sicuri per ottenere il punteggio per una sottoscrizione specifica
- usare **l'API Controlli punteggio** sicuro per elencare i controlli di sicurezza e il punteggio corrente delle sottoscrizioni

Informazioni sugli strumenti esterni resi possibili con l'API punteggio di sicurezza [nell'area del punteggio di sicurezza della community di GitHub.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

Altre informazioni sul [punteggio di sicurezza e i controlli di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Aggiunta di protezioni DNS a livello di Azure Defender per il servizio app

Le operazioni di acquisizione di sottodomini sono una minaccia comune e di gravità elevata per le organizzazioni. L'acquisizione di un sottodominio può verificarsi quando si dispone di un record DNS che punta a un sito Web di cui è stato eseguito il deprovisioning. Tali record DNS sono noti anche come voci "DNS inevase". I record CNAME sono particolarmente vulnerabili a questa minaccia. 

Le acquisizione di sottodomini consentono agli attori di minacce di reindirizzare il traffico destinato al dominio di un'organizzazione a un sito che esegue attività dannose.

Azure Defender servizio app ora rileva le voci DNS inevase quando un sito Web del servizio app viene ritirato. Questo è il momento in cui la voce DNS punta a una risorsa inesistente e il sito Web è vulnerabile all'acquisizione di un sottodominio. Queste protezioni sono disponibili indipendentemente dal fatto che i domini siano gestiti con DNS di Azure o un registrar esterno e si applicino sia al servizio app in Windows che Servizio app in Linux.

Altre informazioni:

- [Tabella di riferimento degli avvisi del](alerts-reference.md#alerts-azureappserv) servizio app: include due nuovi avvisi Azure Defender che vengono attivati quando viene rilevata una voce DNS inevasa
- [Evitare voci DNS inesagiute](../security/fundamentals/subdomain-takeover.md) ed evitare l'acquisizione dell'acquisizione di sottodomini- Informazioni sulla minaccia dell'acquisizione di sottodomini e sull'aspetto del DNS inesagito
- [Introduzione ad Azure Defender per il servizio app](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>I connettori multi-cloud vengono rilasciati per la disponibilità generale (GA)

I carichi di lavoro cloud si estendono in genere su più piattaforme cloud, quindi anche i servizi di sicurezza cloud devono adottare lo stesso approccio.

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

La connessione degli account AWS o GCP integra gli strumenti di sicurezza nativi come AWS Security Hub e GCP Security Command Center in Centro sicurezza di Azure.

Questa funzionalità significa che il Centro sicurezza offre visibilità e protezione in tutti i principali ambienti cloud. Alcuni dei vantaggi di questa integrazione:

- Provisioning automatico degli agenti: il Centro sicurezza usa Azure Arc per distribuire l'agente di Log Analytics nelle istanze di AWS
- Gestione dei criteri
- Gestione vulnerabilità
- Rilevamento di endpoint e risposta incorporato
- Rilevamento degli errori di configurazione per la sicurezza
- Un'unica visualizzazione che mostra le raccomandazioni sulla sicurezza di tutti i provider di servizi cloud
- Incorporare tutte le risorse nei calcoli del punteggio di sicurezza del Centro sicurezza
- Valutazioni della conformità alle normative delle risorse AWS e GCP

Dal menu del Centro sicurezza selezionare **Connettori multi cloud** per visualizzare le opzioni per la creazione di nuovi connettori:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Pulsante Aggiungi un account AWS nella pagina Connettori per più cloud del Centro sicurezza":::

Per altre informazioni, vedere:
- [Connettere gli account AWS a Centro sicurezza di Azure](quickstart-onboard-aws.md)
- [Connettere gli account GCP a Centro sicurezza di Azure](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Esentare intere raccomandazioni dal punteggio di sicurezza per le sottoscrizioni e i gruppi di gestione

Stiamo espandendo la funzionalità di esenzione per includere tutte le raccomandazioni. Offre altre opzioni per ottimizzare le raccomandazioni sulla sicurezza fornite dal Centro sicurezza per le sottoscrizioni, il gruppo di gestione o le risorse.

In alcuni casi, una risorsa verrà elencata come non integra quando si è a sapere che il problema è stato risolto da uno strumento di terze parti che il Centro sicurezza non ha rilevato. Oppure una raccomandazione verrà mostrata in un ambito in cui si è sentito che non appartiene. La raccomandazione potrebbe non essere appropriata per una sottoscrizione specifica. O forse l'organizzazione ha deciso di accettare i rischi correlati alla risorsa o alla raccomandazione specifica.

Con questa funzionalità di anteprima è ora possibile creare un'esenzione per una raccomandazione per:

- **Esentare una** risorsa per assicurarsi che non sia elencata con le risorse non integre in futuro e non influisca sul punteggio di sicurezza. La risorsa verrà elencata come non applicabile e il motivo verrà visualizzato come "esentato" con la giustificazione specifica selezionata.

- **Esentare una sottoscrizione** o un gruppo di gestione per assicurarsi che la raccomandazione non influisca sul punteggio di sicurezza e non verrà visualizzata per la sottoscrizione o il gruppo di gestione in futuro. Ciò si riferisce alle risorse esistenti e a qualsiasi risorsa creata in futuro. La raccomandazione verrà contrassegnata con la giustificazione specifica selezionata per l'ambito selezionato.

Per altre informazioni, [vedere Esenzione di risorse e raccomandazioni dal punteggio di sicurezza.](exempt-resource.md)



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Gli utenti possono ora richiedere visibilità a livello di tenant dall'amministratore globale

Se un utente non ha le autorizzazioni per visualizzare i dati del Centro sicurezza, ora visualizza un collegamento per richiedere le autorizzazioni all'amministratore globale dell'organizzazione. La richiesta include il ruolo che desiderano e la giustificazione per il motivo per cui è necessaria.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner che informa un utente che può richiedere autorizzazioni a livello di tenant.":::

Per altre informazioni, [vedere Richiedere autorizzazioni a livello di tenant quando le proprie sono insufficienti.](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Aggiunte 35 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark

[Azure Security Benchmark è](https://docs.microsoft.com/security/benchmark/azure/introduction) l'iniziativa dei criteri predefinita in Centro sicurezza di Azure. 

Per aumentare la copertura di questo benchmark, al Centro sicurezza sono state aggiunte le 35 raccomandazioni di anteprima seguenti.

> [!TIP]
> Le raccomandazioni in anteprima non contrassegnano una risorsa come non integra e non sono incluse nei calcoli del punteggio di sicurezza. Correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima. Per altre informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

| Controllo di sicurezza                     | Nuove raccomandazioni                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abilita la crittografia dei dati inattivi            | - Gli account Azure Cosmos DB devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi<br>- Le aree di lavoro di Azure Machine Learning devono essere crittografate con una chiave gestita dal cliente<br>- La protezione dei dati BYOK (Bring Your Own Key) deve essere abilitata per i server MySQL<br>- La protezione dei dati BYOK (Bring Your Own Key) deve essere abilitata per i server PostgreSQL<br>- Per gli account di Servizi cognitivi è necessario abilitare la crittografia dei dati con chiave gestita dal cliente<br>- I registri contenitori devono essere crittografati con una chiave gestita dal cliente<br>- Le istanze gestite di SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi<br>- I server SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi<br>- Gli account di archiviazione devono usare la chiave gestita dal cliente per la crittografia                                                                                                                                                              |
| Implementa le procedure consigliate per la sicurezza    | - Per le sottoscrizioni deve essere impostato un indirizzo di posta elettronica di contatto per i problemi relativi alla sicurezza<br> - Il provisioning automatico dell'agente di Log Analytics deve essere abilitato nella sottoscrizione<br> - Le notifiche di posta elettronica devono essere abilitate per gli avvisi con gravità alta<br> - Le notifiche di posta elettronica al proprietario della sottoscrizione devono essere abilitate per gli avvisi con gravità alta<br> - Negli insiemi di credenziali delle chiavi deve essere abilitata la protezione dalla rimozione definitiva<br> - Negli insiemi di credenziali delle chiavi deve essere abilitata la funzionalità di eliminazione temporanea |
| Gestire l'accesso e le autorizzazioni        | - Per le app per le funzioni deve essere abilitata l'opzione 'Certificati client (certificati client in ingresso)' |
| Proteggi le applicazioni da attacchi DDoS | - Web Application Firewall (WAF) deve essere abilitato per il gateway applicazione<br> - Web Application Firewall (WAF) deve essere abilitato per il servizio Frontdoor di Azure |
| Limita l'accesso non autorizzato alla rete | - Il firewall deve essere abilitato in Key Vault<br> - L'endpoint privato deve essere configurato per Key Vault<br> - Configurazione app deve usare collegamenti privati<br> - La cache di Azure per Redis deve risiedere all'interno di una rete virtuale<br> - I domini di Griglia di eventi di Azure devono usare collegamenti privati<br> - Gli argomenti di Griglia di eventi di Azure devono usare collegamenti privati<br> - Le aree di lavoro di Azure Machine Learning devono usare collegamenti privati<br> - Il servizio Azure SignalR deve usare collegamenti privati<br> - Azure Spring Cloud deve usare l'aggiunta alla rete<br> - I registri contenitori non devono consentire l'accesso alla rete senza restrizioni<br> - I registri contenitori devono usare collegamenti privati<br> - L'accesso alla rete pubblica deve essere disabilitato per i server MariaDB<br> - L'accesso alla rete pubblica deve essere disabilitato per i server MySQL<br> - L'accesso alla rete pubblica deve essere disabilitato per i server PostgreSQL<br> - L'account di archiviazione deve usare una connessione collegamento privato<br> - Gli account di archiviazione devono limitare l'accesso alla rete usando regole di rete virtuale<br> - I modelli di Image Builder per macchine virtuali devono usare un collegamento privato|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Collegamenti correlati:

- [Altre informazioni su Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Altre informazioni su Database di Azure per MariaDB](../mariadb/overview.md)
- [Altre informazioni su Database di Azure per MySQL](../mysql/overview.md)
- [Altre informazioni su Database di Azure per PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Esportazione in CSV dell'elenco filtrato di raccomandazioni 

A novembre 2020 sono stati aggiunti i filtri alla pagina Raccomandazioni (vedere [L'elenco di raccomandazioni ora include i filtri](#recommendations-list-now-includes-filters)). A dicembre questi filtri sono stati ampliati ([La pagina Raccomandazioni contiene nuovi filtri per ambiente, gravità e risposte disponibili](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Con questo annuncio, il comportamento del **pulsante Scarica in CSV** è stato cambiato in modo che l'esportazione in CSV includa solo le raccomandazioni attualmente visualizzate nell'elenco filtrato. 

Nell'immagine seguente, ad esempio, è possibile vedere che l'elenco è stato filtrato per includere due raccomandazioni. Il file CSV generato include i dettagli sullo stato di ogni risorsa interessata da queste due raccomandazioni.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Esportazione di raccomandazioni filtrate in un file CSV":::

Per altre informazioni, vedere [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Le risorse "Non applicabili" vengono ora segnalate come "conformi" Criteri di Azure valutazione

In precedenza, le risorse valutate  per una raccomandazione e che non erano applicabili venivano Criteri di Azure come "Non conformi". Nessuna azione dell'utente può modificare il proprio stato in "Conforme". Con questa modifica, vengono segnalati come "conformi" per maggiore chiarezza.

L'unico effetto si vedrà in Criteri di Azure, dove il numero di risorse conformi aumenterà. Non ci sarà alcun impatto sul punteggio di sicurezza nel Centro sicurezza di Azure.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Esportare snapshot settimanali dei dati relativi al punteggio di sicurezza e alla conformità alle normative con l'esportazione continua (anteprima)

È stata aggiunta una nuova [](continuous-export.md) funzionalità di anteprima agli strumenti di esportazione continua per l'esportazione di snapshot settimanali del punteggio di sicurezza e dei dati di conformità alle normative.

Quando si definisce un'esportazione continua, impostare la frequenza di esportazione:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Scelta della frequenza dell'esportazione continua":::

- **Streaming:** le valutazioni verranno inviate in tempo reale quando viene aggiornato lo stato di integrità di una risorsa (se non vengono eseguiti aggiornamenti, non verranno inviati dati).
- **Snapshot: uno** snapshot dello stato corrente di tutte le valutazioni di conformità alle normative verrà inviato ogni settimana (si tratta di una funzionalità di anteprima per gli snapshot settimanali dei punteggi di sicurezza e dei dati di conformità alle normative).

Per altre informazioni sulle funzionalità complete di questa funzionalità, vedere Esportare continuamente i [dati del Centro sicurezza.](continuous-export.md)

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
- **Azure Defender per SQL Server** nei computer: estende le stesse protezioni ai server SQL in ambienti ibridi, multi-cloud e locali

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

Per assegnare a se stessi le autorizzazioni a livello di tenant, seguire le istruzioni riportate in [Concedere autorizzazioni a livello di tenant a se stessi](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).


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
- **Opzioni per gli operatori** per ogni filtro. È ora possibile scegliere tra più operatori logici diversi da '='. Ad esempio, è possibile trovare tutte le risorse con raccomandazioni attive il cui titolo include la stringa "crittografare". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Controlli dell'opzione Operatore nei filtri dell'inventario degli asset":::

Per altre informazioni sull'inventario, vedere [Esplorare e gestire le risorse con l'inventario degli asset](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>La raccomandazione relativa alla richiesta di certificati SSL da parte delle app Web non fa più parte del punteggio di sicurezza

La raccomandazione "È consigliabile che le app Web richiedano un certificato SSL per tutte le richieste in ingresso" è stata spostata dal controllo di sicurezza **Gestisci l'accesso e le autorizzazioni** (che vale un massimo di 4 punti) al controllo **Implementa le procedure consigliate per la sicurezza** (che non vale alcun punto). 

Garantire che un'app Web richiede un certificato lo rende sicuramente più sicuro. Questa impostazione è tuttavia irrilevante per le app Web pubbliche. se si accede al sito tramite HTTP e non HTTPS, non si riceveranno i certificati client. Pertanto, se l'applicazione richiede i certificati client, è consigliabile non consentire le richieste all'applicazione tramite HTTP. Per altre informazioni, vedere [Configurare l'autenticazione reciproca TLS per Servizio app di Azure](../app-service/app-service-web-configure-tls-mutual-auth.md).

Con questa modifica, la raccomandazione è ora una procedura consigliata che non influisce sul punteggio. 

Per informazioni sulle raccomandazioni disponibili in ogni controllo di sicurezza, vedere [Controlli di sicurezza e relative raccomandazioni](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>La pagina Raccomandazioni contiene nuovi filtri per ambiente, gravità e risposte disponibili

Il Centro sicurezza di Azure monitora tutte le risorse connesse e genera raccomandazioni sulla sicurezza. Usare queste raccomandazioni per rafforzare il comportamento del cloud ibrido e monitorare la conformità con i criteri e gli standard pertinenti per la propria organizzazione, il proprio settore e il proprio paese.

L'elenco delle raccomandazioni sulla sicurezza cresce ogni mese, man mano che il Centro sicurezza continua a espandere la propria copertura e le funzionalità. Ad esempio, vedere [Aggiunte 29 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Con l'elenco in crescita, è necessario filtrare le raccomandazioni per trovare quelle di maggiore interesse. Lo scorso novembre sono stati aggiunti i filtri alla pagina Raccomandazioni (vedere [L'elenco di raccomandazioni ora include i filtri](#recommendations-list-now-includes-filters)).

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

    - **Verificare se la configurazione è abilitata.** Se non è abilitata, il criterio visualizza uno stato non conforme e crea una risorsa conforme. Altre informazioni sui modelli di Criteri di Azure disponibili sono disponibili nella scheda "Distribuisci su larga scala con Criteri di Azure" in [Configurare un'esportazione continua.](continuous-export.md#set-up-a-continuous-export)

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
- [La raccomandazione "Gli aggiornamenti di sistema devono essere installati nei computer" include ora le sottorecommendazioni](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [La pagina di gestione dei criteri nel portale di Azure ora mostra lo stato delle assegnazioni di criteri predefiniti](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Aggiunte 29 raccomandazioni di anteprima per aumentare la copertura di Azure Security Benchmark

Azure Security Benchmark è il set di linee guida per la sicurezza e la conformità basato su framework di conformità comuni creati da Microsoft e specifici di Azure. [Altre informazioni su Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

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

- [Altre informazioni su Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Altre informazioni sulle app per le API di Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Altre informazioni sulle app per le funzioni di Azure](../azure-functions/functions-overview.md)
- [Altre informazioni sulle app Web di Azure](../app-service/overview.md)
- [Altre informazioni su Database di Azure per MariaDB](../mariadb/overview.md)
- [Altre informazioni su Database di Azure per MySQL](../mysql/overview.md)
- [Altre informazioni su Database di Azure per PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>Aggiunta di NIST SP 800 171 R2 al dashboard di conformità alle normative del Centro sicurezza

Lo standard NIST SP 800-171 R2 è ora disponibile come iniziativa predefinita per l'uso nel dashboard di conformità alle normative del Centro sicurezza di Azure. I mapping per i controlli sono descritti in [Dettagli dell'iniziativa predefinita di conformità alle normative per NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md). 

Per applicare lo standard alle sottoscrizioni e monitorare continuamente lo stato di conformità, seguire le istruzioni in Personalizzare il set di [standard nel dashboard di conformità alle normative.](update-regulatory-compliance-packages.md)

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

Con la crescita del Centro sicurezza di Azure, sono state sviluppate più estensioni ed è ora possibile monitorare un elenco più lungo di tipi di risorsa. Gli strumenti di provisioning automatico sono stati ora espansi per supportare altre estensioni e tipi di risorse sfruttando le funzionalità di Criteri di Azure.

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


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>La raccomandazione "Gli aggiornamenti di sistema devono essere installati nei computer" include ora le sottorecommendazioni

La raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali** è stata ottimizzata. La nuova versione include sottorecommendazioni per ogni aggiornamento mancante e apporta i miglioramenti seguenti:

- Un'esperienza riprogettata nelle pagine del Centro sicurezza di Azure del portale di Azure. La pagina di dettagli della raccomandazione **Gli aggiornamenti di sistema devono essere installati nelle macchine virtuali** include l'elenco di risultati, come illustrato di seguito. Quando si seleziona un singolo risultato, viene visualizzato il riquadro dei dettagli con un collegamento alle informazioni sulla correzione e un elenco delle risorse interessate.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Apertura di una delle sottocommendazioni nell'esperienza del portale per la raccomandazione aggiornata":::

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