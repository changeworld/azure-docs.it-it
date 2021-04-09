---
title: Come pianificare e distribuire i server abilitati per Azure Arc
description: Informazioni su come abilitare un numero elevato di computer per i server abilitati per Azure Arc per semplificare la configurazione delle funzionalità di sicurezza, gestione e monitoraggio essenziali in Azure.
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: 5aa7022dba943fa3de247404522408f4660e80e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023283"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Pianificare e distribuire i server abilitati per Arc

La distribuzione di un servizio di infrastruttura IT o di un'applicazione aziendale è una sfida per qualsiasi azienda. Per eseguire questa procedura ed evitare eventuali sorprese sgradite e costi non pianificati, è necessario pianificarlo accuratamente per assicurarsi di essere il più pronto possibile. Per pianificare la distribuzione di server abilitati per Azure Arc su qualsiasi scala, è necessario soddisfare i criteri di progettazione e distribuzione che devono essere soddisfatti per completare correttamente le attività.

Affinché la distribuzione possa procedere senza problemi, il piano deve stabilire una chiara comprensione di:

* Ruoli e responsabilità.
* Inventario di server fisici o macchine virtuali per verificare che soddisfino i requisiti di rete e di sistema.
* Il set di competenze e il training necessari per consentire una corretta distribuzione e una gestione continua.
* Criteri di accettazione e il modo in cui si tiene traccia dell'esito positivo.
* Strumenti o metodi da usare per automatizzare le distribuzioni.
* Rischi e piani di mitigazione identificati per evitare ritardi, le rotture e così via.
* Come evitare l'interferenza durante la distribuzione.
* Qual è il percorso di escalation quando si verifica un problema significativo?

Lo scopo di questo articolo è assicurarsi di essere pronti per una distribuzione corretta dei server abilitati per Azure Arc su più server fisici di produzione o macchine virtuali nell'ambiente in uso.

## <a name="prerequisites"></a>Prerequisiti

* I computer eseguono un [sistema operativo supportato](agent-overview.md#supported-operating-systems) per l'agente del computer connesso.
* I computer dispongono di connettività dalla rete locale o da un altro ambiente cloud alle risorse in Azure, direttamente o tramite un server proxy.
* Per installare e configurare l'agente del computer connesso di Arc Enabled Servers, un account con privilegi elevati (ovvero, un amministratore o root) sui computer.
* Per eseguire l'onboarding di computer, è necessario essere membri del ruolo **Onboarding di computer connessi di Azure**.
* Per leggere, modificare ed eliminare un computer, si è membri del ruolo di **amministratore delle risorse del computer connesso di Azure** .

## <a name="pilot"></a>Fase pilota

Prima di eseguire la distribuzione in tutti i computer di produzione, iniziare valutando questo processo di distribuzione prima di adottarlo in modo esteso nell'ambiente in uso. Per un progetto pilota, identificare un campionamento rappresentativo di computer che non sono cruciali per le aziende in grado di condurre le attività aziendali. È opportuno assicurarsi di concedere tempo sufficiente per eseguire il progetto pilota e valutarne l'effetto: è consigliabile un minimo di 30 giorni.

Definire un piano formale che descriva l'ambito e i dettagli del progetto pilota. Di seguito è riportato un esempio di ciò che deve essere incluso in un piano per iniziare.

* Obiettivi: descrive i driver aziendali e tecnici che hanno portato alla decisione che un progetto pilota è necessario.
* Criteri di selezione: specifica i criteri usati per selezionare gli aspetti della soluzione che verranno illustrati tramite un progetto pilota.
* Ambito: descrive l'ambito del progetto pilota, che include, tra l'altro, i componenti della soluzione, la pianificazione prevista, la durata del progetto pilota e il numero di computer di destinazione.
* Criteri e metriche di successo: definire i criteri di successo del pilota e misure specifiche usate per determinare il livello di successo.
* Piano di formazione: descrive il piano per i tecnici del sistema di formazione, gli amministratori e così via, che non hanno familiarità con Azure e i servizi IT durante la fase pilota.
* Piano di transizione: descrive la strategia e i criteri usati per guidare la transizione dal progetto pilota alla produzione.
* Rollback: descrive le procedure per eseguire il rollback di un progetto pilota allo stato di pre-distribuzione.
* Rischi: elenca tutti i rischi identificati per condurre il progetto pilota e associato alla distribuzione di produzione.

## <a name="phase-1-build-a-foundation"></a>Fase 1: creare una base

In questa fase, i tecnici o gli amministratori di sistema abilitano le funzionalità principali della sottoscrizione di Azure per le organizzazioni per avviare la base prima di abilitare i computer per la gestione da parte di server abilitati per Arc e altri servizi di Azure.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
| [Creare un gruppo di risorse](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Un gruppo di risorse dedicato per includere solo i server abilitati per Arc e centralizzare la gestione e il monitoraggio di queste risorse. | Un'ora |
| Applicare i [tag](../../azure-resource-manager/management/tag-resources.md) per organizzare i computer. | Valutazione e sviluppo di una strategia di [assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) allineata all'IT che consente di ridurre la complessità della gestione dei server abilitati per Arc e semplificare le decisioni di gestione. | Un giorno |
| Progettare e distribuire [log di monitoraggio di Azure](../../azure-monitor/logs/data-platform-logs.md) | Valutare le [considerazioni relative alla progettazione e alla distribuzione](../../azure-monitor/logs/design-logs-deployment.md) per determinare se l'organizzazione deve usare una esistente o implementare un'altra area di lavoro log Analytics per archiviare i dati di log raccolti da computer e server ibridi. <sup>1</sup> | Un giorno |
| [Sviluppare un piano di governance dei criteri di Azure](../../governance/policy/overview.md) | Determinare come si implementerà la governance dei computer e dei server ibridi nell'ambito della sottoscrizione o del gruppo di risorse con criteri di Azure. | Un giorno |
| Configurare il [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) | Sviluppare un piano di accesso per controllare chi ha accesso per gestire i server abilitati per Arc e la possibilità di visualizzare i dati da altri servizi e soluzioni di Azure. | Un giorno |
| Identificare i computer in cui è già installato Log Analytics Agent | Eseguire la query di log seguente in [log Analytics](../../azure-monitor/logs/log-analytics-overview.md) per supportare la conversione di distribuzioni di agenti log Analytics esistenti nell'agente gestito dall'estensione:<br> Heartbeat <br> &#124; dove TimeGenerated > fa (30D) <br> &#124; where ResourceType = = "Machines" e (ComputerEnvironment = = "non-Azure") <br> &#124; riepilogare per computer, ResourceProvider, ResourceType, ComputerEnvironment | Un'ora |

<sup>1</sup> una considerazione importante nell'ambito della valutazione della progettazione dell'area di lavoro log Analytics è l'integrazione con automazione di Azure a supporto della funzionalità Gestione aggiornamenti e rilevamento modifiche e dell'inventario, nonché del Centro sicurezza di Azure e di Azure Sentinel. Se l'organizzazione dispone già di un account di automazione e Abilita le funzionalità di gestione collegate a un'area di lavoro Log Analytics, valutare se è possibile centralizzare e semplificare le operazioni di gestione, nonché ridurre i costi, usando le risorse esistenti rispetto alla creazione di un account duplicato, un'area di lavoro e così via.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fase 2: distribuire i server abilitati per Arc

A questo punto, viene aggiunto al fondamento definito nella fase 1 preparando e distribuendo l'agente computer connesso dei server con Arc Enabled.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
| Scaricare lo script di installazione predefinito | Esaminare e personalizzare lo script di installazione predefinito per la distribuzione su larga scala dell'agente del computer connesso per supportare i requisiti di distribuzione automatica.<br><br> Esempio di risorse di onboarding su larga scala:<br><br> <ul><li> [Script di distribuzione di base su larga scala](onboard-service-principal.md)</ul></li> <ul><li>[Onboarding su larga scala VMware vSphere macchine virtuali Windows Server](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Onboarding su scala VMware vSphere VM Linux](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Caricamento a livello di istanze di AWS EC2 con Ansible](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Distribuzione su larga scala tramite la comunicazione remota di PowerShell](./onboard-powershell.md) (solo Windows)</ul></li>| Uno o più giorni, a seconda dei requisiti, dei processi aziendali (ad esempio, delle modifiche e delle Release Management) e del metodo di automazione utilizzato. |
| [Creare un'entità servizio](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Creare un'entità servizio per connettere i computer in modo non interattivo usando Azure PowerShell o dal portale.| Un'ora |
| Distribuire l'agente del computer connesso ai server e ai computer di destinazione |Usare lo strumento di automazione per distribuire gli script nei server e connetterli ad Azure.| Uno o più giorni a seconda del piano di rilascio e se si segue un'implementazione graduale. |

## <a name="phase-3-manage-and-operate"></a>Fase 3: gestione e funzionamento

La fase 3 consente agli amministratori o ai tecnici di sistema di automatizzare le attività manuali per gestire e utilizzare l'agente del computer connesso e il computer durante il ciclo di vita.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
|Creazione di un avviso Integrità risorse |Se un server smette di inviare heartbeat ad Azure per più di 15 minuti, può significare che è offline, che la connessione di rete è stata bloccata o che l'agente non è in esecuzione. Sviluppare un piano per il modo in cui rispondere ed esaminare questi eventi imprevisti e utilizzare [integrità risorse avvisi](../..//service-health/resource-health-alert-monitor-guide.md) per ricevere una notifica all'avvio.<br><br> Quando si configura l'avviso, specificare quanto segue:<br> **Tipo**  =  di risorsa **Server abilitati per Azure Arc**<br> Stato corrente della **risorsa**  =  Non **disponibile**<br> **Stato**  =  risorsa precedente **Disponibile** | Un'ora |
|Creare un avviso Azure Advisor | Per un'esperienza ottimale e per la sicurezza e le correzioni di bug più recenti, è consigliabile mantenere aggiornati gli agenti dei server abilitati per Azure Arc. Gli agenti non aggiornati verranno identificati con un [avviso Azure Advisor](../../advisor/advisor-alerts-portal.md).<br><br> Quando si configura l'avviso, specificare quanto segue:<br> **Tipo**  =  di raccomandazione **Eseguire l'aggiornamento alla versione più recente dell'agente del computer connesso di Azure** | Un'ora |
|[Assegnare criteri di Azure](../../governance/policy/assign-policy-portal.md) all'ambito della sottoscrizione o del gruppo di risorse |Assegnare il [criterio](../../azure-monitor/vm/vminsights-enable-policy.md) **Enable monitoraggio di Azure per le macchine virtuali** (e altri utenti che soddisfano le proprie esigenze) all'ambito della sottoscrizione o del gruppo di risorse. Criteri di Azure consente di assegnare le definizioni dei criteri che installano gli agenti necessari per Monitoraggio di Azure per le macchine virtuali nell'ambiente in uso.| Varia |
|[Abilitare Gestione aggiornamenti per i server abilitati per Arc](../../automation/update-management/enable-from-automation-account.md) |Configurare Gestione aggiornamenti in automazione di Azure per gestire gli aggiornamenti del sistema operativo per le macchine virtuali Windows e Linux registrate con i server abilitati per Arc. | 15 minuti |

## <a name="next-steps"></a>Passaggi successivi

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come semplificare la distribuzione con altri servizi di Azure, come la [configurazione dello stato](../../automation/automation-dsc-overview.md) di automazione di Azure e altre [estensioni di VM di Azure](manage-vm-extensions.md)supportate.