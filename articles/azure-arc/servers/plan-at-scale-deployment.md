---
title: Come pianificare e distribuire Azure Arc server abilitati
description: Informazioni su come abilitare un numero elevato di computer per Azure Arc server abilitati per semplificare la configurazione delle funzionalità essenziali di sicurezza, gestione e monitoraggio in Azure.
ms.date: 04/21/2021
ms.topic: conceptual
ms.openlocfilehash: e3f8fe410da56f627ceab5f17c980f2daa1a262c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831979"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Pianificare e distribuire server abilitati per Arc

La distribuzione di un servizio di infrastruttura IT o di un'applicazione aziendale è una sfida per qualsiasi azienda. Per eseguirlo in modo corretto ed evitare sorprese indesiderate e costi non pianificati, è necessario pianificarlo accuratamente per assicurarsi di essere il più pronti possibile. Per pianificare la distribuzione Azure Arc server abilitati su qualsiasi scala, è necessario soddisfare i criteri di progettazione e distribuzione che devono essere soddisfatti per completare correttamente le attività.

Perché la distribuzione proceda senza problemi, il piano deve stabilire una chiara comprensione di:

* Ruoli e responsabilità.
* Inventario di server fisici o macchine virtuali per verificare che soddisfino i requisiti di rete e di sistema.
* Set di competenze e training necessari per consentire la corretta distribuzione e la gestione in corso.
* Criteri di accettazione e modalità di rilevamento dell'esito positivo.
* Strumenti o metodi da usare per automatizzare le distribuzioni.
* Sono stati identificati i rischi e i piani di mitigazione per evitare ritardi, interruzioni e così via.
* Come evitare interruzioni durante la distribuzione.
* Qual è il percorso di escalation quando si verifica un problema significativo?

Lo scopo di questo articolo è assicurarsi di essere pronti per una corretta distribuzione di Azure Arc server abilitati tra più server fisici di produzione o macchine virtuali nell'ambiente.

## <a name="prerequisites"></a>Prerequisiti

* I computer eseguono un [sistema operativo supportato per](agent-overview.md#supported-operating-systems) l'agente Connected Machine.
* I computer hanno connettività dalla rete locale o da un altro ambiente cloud alle risorse in Azure, direttamente o tramite un server proxy.
* Per installare e configurare l'agente Connected Machine dei server abilitati per Arc, un account con privilegi elevati (ovvero un amministratore o come radice) nei computer.
* Per eseguire l'onboarding di computer, è necessario essere membri del ruolo **Onboarding di computer connessi di Azure**.
* Per leggere, modificare ed eliminare un computer, si è membri del ruolo **amministratore Azure Connected Machine** risorse.

## <a name="pilot"></a>Fase pilota

Prima di eseguire la distribuzione in tutti i computer di produzione, iniziare valutando questo processo di distribuzione prima di adottarlo su vasta gamma nell'ambiente. Per un progetto pilota, identificare un campionamento rappresentativo di computer che non sono fondamentali per la capacità aziendale di svolgere attività aziendali. È opportuno assicurarsi di concedere tempo sufficiente per eseguire il progetto pilota e valutarne l'impatto: è consigliabile un minimo di 30 giorni.

Definire un piano formale che descriva l'ambito e i dettagli del progetto pilota. Di seguito è riportato un esempio di ciò che un piano deve includere per iniziare.

* Obiettivi: descrive i driver aziendali e tecnici che hanno portato alla decisione che un progetto pilota è necessario.
* Criteri di selezione: specifica i criteri usati per selezionare gli aspetti della soluzione che verranno illustrati tramite un progetto pilota.
* Ambito: descrive l'ambito del progetto pilota, che include, a parte i componenti della soluzione, la pianificazione prevista, la durata del progetto pilota e il numero di computer di destinazione.
* Criteri e metriche di successo: definire i criteri di successo del progetto pilota e le misure specifiche usate per determinare il livello di successo.
* Piano di training: descrive il piano per i tecnici del sistema di formazione, gli amministratori e così via che non hanno esperienza con Azure e i servizi it durante il progetto pilota.
* Piano di transizione: descrive la strategia e i criteri usati per guidare la transizione dal progetto pilota alla produzione.
* Rollback: descrive le procedure per eseguire il rollback di un progetto pilota allo stato di pre-distribuzione.
* Rischi: elencare tutti i rischi identificati per l'esecuzione del progetto pilota e associati alla distribuzione di produzione.

## <a name="phase-1-build-a-foundation"></a>Fase 1: Creare una base

In questa fase, i tecnici o gli amministratori di sistema abilitano le funzionalità di base nella sottoscrizione di Azure dell'organizzazione per iniziare a creare le basi prima di abilitare i computer per la gestione tramite server abilitati per Arc e altri servizi di Azure.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
| [Creare un gruppo di risorse](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Un gruppo di risorse dedicato per includere solo i server abilitati per Arc e centralizzare la gestione e il monitoraggio di queste risorse. | Un'ora |
| Applicare [tag per](../../azure-resource-manager/management/tag-resources.md) organizzare i computer. | Valutare e sviluppare una strategia di assegnazione di [tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) allineata all'IT che consente di ridurre la complessità della gestione dei server abilitati per Arc e semplificare le decisioni di gestione. | Un giorno |
| Progettare e distribuire [Monitoraggio di Azure log](../../azure-monitor/logs/data-platform-logs.md) | Valutare [le considerazioni di progettazione e distribuzione](../../azure-monitor/logs/design-logs-deployment.md) per determinare se l'organizzazione deve usare un'area di lavoro Log Analytics esistente o implementare un'altra per archiviare i dati di log raccolti da server e computer ibridi. <sup>1</sup> | Un giorno |
| [Sviluppare un piano Criteri di Azure](../../governance/policy/overview.md) governance | Determinare come implementare la governance di server e computer ibridi nell'ambito della sottoscrizione o del gruppo di risorse con Criteri di Azure. | Un giorno |
| Configurare il [controllo degli accessi in base al](../../role-based-access-control/overview.md) ruolo | Sviluppare un piano di accesso per controllare chi può accedere per gestire i server abilitati per Arc e la possibilità di visualizzare i dati da altri servizi e soluzioni di Azure. | Un giorno |
| Identificare i computer con l'agente di Log Analytics già installato | Eseguire la query di log seguente in [Log Analytics per](../../azure-monitor/logs/log-analytics-overview.md) supportare la conversione delle distribuzioni dell'agente di Log Analytics esistenti in un agente gestito dall'estensione:<br> Heartbeat <br> &#124; dove TimeGenerated > ago(30d) <br> &#124; dove ResourceType == "machines" e (ComputerEnvironment == "Non-Azure") <br> &#124; riepilogo per Computer, ResourceProvider, ResourceType, ComputerEnvironment | Un'ora |

<sup>1</sup> Un aspetto importante della valutazione della progettazione dell'area di lavoro Log Analytics è l'integrazione con Automazione di Azure a supporto della funzionalità Gestione aggiornamenti e Rilevamento modifiche e inventario, nonché di Centro sicurezza di Azure e Azure Sentinel. Se l'organizzazione ha già un account di Automazione e ha abilitato le funzionalità di gestione collegate a un'area di lavoro Log Analytics, valutare se è possibile centralizzare e semplificare le operazioni di gestione, nonché ridurre al minimo i costi, usando tali risorse esistenti anziché creando un account duplicato, un'area di lavoro e così via.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fase 2: Distribuire server abilitati per Arc

Si aggiunge quindi alla base prevista nella fase 1, preparando e distribuendo l'agente Connected Machine dei server abilitati per Arc.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
| Scaricare lo script di installazione predefinito | Esaminare e personalizzare lo script di installazione predefinito per la distribuzione su larga scala dell'agente Connected Machine per supportare i requisiti di distribuzione automatizzata.<br><br> Esempio di risorse di onboarding su larga scala:<br><br> <ul><li> [Script di distribuzione di base su larga scala](onboard-service-principal.md)</ul></li> <ul><li>[Onboarding su larga scala VMware vSphere macchine virtuali Windows Server](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Onboarding su larga scala VMware vSphere macchine virtuali Linux](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Onboarding su larga scala delle istanze EC2 di AWS con Ansible](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Distribuzione su larga scala tramite la comunicazione remota di PowerShell](./onboard-powershell.md) (solo Windows)</ul></li>| Uno o più giorni a seconda dei requisiti, dei processi organizzativi (ad esempio, Modifica e Release Management) e del metodo di automazione usato. |
| [Creare un'entità servizio](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Creare un'entità servizio per connettere i computer in modo non interattivo usando Azure PowerShell o dal portale.| Un'ora |
| Distribuire l'agente Connected Machine nei server e nei computer di destinazione |Usare lo strumento di automazione per distribuire gli script nei server e connetterli ad Azure.| Uno o più giorni a seconda del piano di rilascio e se si segue un'implementazione graduale. |

## <a name="phase-3-manage-and-operate"></a>Fase 3: Gestire e gestire

Nella fase 3 gli amministratori o i tecnici del sistema consentono l'automazione delle attività manuali per gestire e usare l'agente Connected Machine e il computer durante il ciclo di vita.

|Attività |Dettaglio |Duration |
|-----|-------|---------|
|Creare un avviso Integrità risorse avviso |Se un server smette di inviare heartbeat ad Azure per più di 15 minuti, può significare che è offline, che la connessione di rete è stata bloccata o che l'agente non è in esecuzione. Sviluppare un piano per la risposta, analizzare questi [](../..//service-health/resource-health-alert-monitor-guide.md) eventi imprevisti e usare gli avvisi Integrità risorse ricevere una notifica all'avvio.<br><br> Quando si configura l'avviso, specificare quanto segue:<br> **Tipo di risorsa**  =  **Azure Arc server abilitati**<br> **Stato corrente della risorsa**  =  **Non disponibile**<br> **Stato precedente della risorsa**  =  **Disponibile** | Un'ora |
|Creare un Azure Advisor avviso | Per un'esperienza ottimale e le correzioni di bug e sicurezza più recenti, è consigliabile mantenere aggiornato Azure Arc server abilitati. Gli agenti non aggiornati verranno identificati con un Azure Advisor [avviso](../../advisor/advisor-alerts-portal.md).<br><br> Quando si configura l'avviso, specificare quanto segue:<br> **Tipo di raccomandazione**  =  **Eseguire l'aggiornamento alla versione più recente del agente di Azure Connected Machine** | Un'ora |
|[Assegnare criteri di Azure](../../governance/policy/assign-policy-portal.md) all'ambito della sottoscrizione o del gruppo di risorse |Assegnare **il criterio Abilita Monitoraggio di Azure per le macchine virtuali** [criteri](../../azure-monitor/vm/vminsights-enable-policy.md) (e altri che soddisfano le esigenze) all'ambito della sottoscrizione o del gruppo di risorse. Criteri di Azure consente di assegnare definizioni di criteri che installano gli agenti necessari per le informazioni dettagliate sulle macchine virtuali nell'ambiente.| Varia |
|[Abilitare Gestione aggiornamenti per i server abilitati per Arc](../../automation/update-management/enable-from-automation-account.md) |Configurare Gestione aggiornamenti in Automazione di Azure per gestire gli aggiornamenti del sistema operativo per le macchine virtuali Windows e Linux registrate con i server abilitati per Arc. | 15 minuti |

## <a name="next-steps"></a>Passaggi successivi

* Le informazioni sulla risoluzione dei problemi sono disponibili nella Guida [all'agente di Risoluzione dei problemi di Connected Machine.](troubleshoot-agent-onboard.md)

* Informazioni su come semplificare la distribuzione con altri servizi di Azure come Automazione di Azure [State Configuration](../../automation/automation-dsc-overview.md) e altre estensioni [di macchine virtuali di Azure supportate.](manage-vm-extensions.md)