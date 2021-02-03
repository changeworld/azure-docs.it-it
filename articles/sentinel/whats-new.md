---
title: Novità di Azure Sentinel
description: Questo articolo descrive le nuove funzionalità di Azure Sentinel risalenti agli ultimi mesi.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 8154e1adff8d8c2bdfe7fedc9309f95e5c5880bd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500721"
---
# <a name="whats-new-in-azure-sentinel"></a>Novità di Azure Sentinel

Questo articolo elenca le funzionalità recenti aggiunte a Sentinel di Azure e le nuove funzionalità dei servizi correlati che offrono un'esperienza utente migliorata in Sentinel di Azure.

Per informazioni sulle funzionalità precedenti fornite, vedere i [Blog della community tecnica](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Le funzionalità indicate sono attualmente in anteprima. Le [condizioni aggiuntive per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, anteprima o diversamente non ancora disponibili a livello generale.


> [!TIP]
> I nostri team di caccia alle minacce di Microsoft contribuiscono a eseguire query, PlayBook, cartelle di lavoro e notebook nella [community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel), incluse [query di caccia](https://github.com/Azure/Azure-Sentinel) specifiche che i team possono adattare e usare. 
>
> È anche possibile contribuire. Partecipa alla [community GitHub di Azure Sentinel Threat Hunters](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="january-2021"></a>Gennaio 2021

- [AZ. SecurityInsights PowerShell Module (anteprima pubblica)](#azsecurityinsights-powershell-module-public-preview)
- [Connettore database SQL](#sql-database-connector)
- [Commenti per gli eventi imprevisti migliorati](#improved-incident-comments)
- [Cluster Log Analytics dedicati](#dedicated-log-analytics-clusters)
- [Identità gestite di app per la logica](#logic-apps-managed-identities)
- [Ottimizzazione della regola migliorata con i grafici di anteprima della regola di analisi](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="azsecurityinsights-powershell-module-public-preview"></a>AZ. SecurityInsights PowerShell Module (anteprima pubblica)

Azure Sentinel supporta ora il nuovo modulo di PowerShell [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) .

Il modulo **AZ. SecurityInsights** supporta i casi d'uso comuni di Azure Sentinel, ad esempio l'interazione con gli eventi imprevisti per modificare le statue, la gravità, il proprietario e così via, l'aggiunta di commenti ed etichette agli eventi imprevisti e la creazione di segnalibri.

Sebbene sia consigliabile usare modelli di [Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/) per la pipeline di integrazione continua/recapito continuo, il modulo **AZ. SecurityInsights** è utile per le attività post-distribuzione ed è destinato specificamente all'automazione Soc.  Ad esempio, l'automazione SOC potrebbe includere passaggi per configurare i connettori di dati, creare regole di analisi o aggiungere azioni di automazione alle regole di analisi.

Per ulteriori informazioni, tra cui un elenco completo e una descrizione dei cmdlet disponibili, delle descrizioni dei parametri e degli esempi, vedere la [documentazione di PowerShell AZ. SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Connettore database SQL

Azure Sentinel offre ora un connettore del database SQL di Azure, in cui è possibile trasmettere in streaming i log di controllo e di diagnostica dei database in Sentinel di Azure e monitorare continuamente l'attività in tutte le istanze.

Azure SQL è un motore di database PaaS (Platform-as-a-Service) completamente gestito che gestisce la maggior parte delle funzioni di gestione di database, ad esempio l'aggiornamento, l'applicazione di patch, i backup e il monitoraggio, senza coinvolgimento degli utenti.

Per altre informazioni, vedere [connettere i log di diagnostica e di controllo del database SQL di Azure](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Commenti per gli eventi imprevisti migliorati

Gli analisti usano i commenti per gli eventi imprevisti per collaborare agli eventi imprevisti, documentare i processi e i passaggi manualmente o come parte di un PlayBook. 

L'esperienza migliorata per i commenti degli eventi imprevisti consente di formattare i commenti e modificare o eliminare i commenti esistenti.

Per ulteriori informazioni, vedere [creazione automatica degli eventi imprevisti da avvisi di sicurezza Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Cluster Log Analytics dedicati

Azure Sentinel supporta ora cluster di Log Analytics dedicati come opzione di distribuzione. Si consiglia di prendere in considerazione un cluster dedicato se:

- Inserire **oltre 1 TB al giorno** nell'area di lavoro di Azure Sentinel
- **Avere più aree di lavoro di Azure Sentinel** nella registrazione di Azure

I cluster dedicati consentono di usare funzionalità quali chiavi gestite dal cliente, archivio protetto, crittografia doppia e query tra aree di lavoro più veloci quando si dispone di più aree di lavoro nello stesso cluster.

Per ulteriori informazioni, vedere [log di monitoraggio di Azure cluster dedicati](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Identità gestite di app per la logica

Azure Sentinel supporta ora le identità gestite per il connettore Azure Sentinel per le app per la logica, consentendo di concedere le autorizzazioni direttamente a un PlayBook specifico per operare su Sentinel di Azure anziché creare identità aggiuntive.

- **Senza un'identità gestita**, il connettore app per la logica richiede un'identità separata con un ruolo controllo degli accessi in base al ruolo di Azure Sentinel per l'esecuzione in Sentinel di Azure. L'identità separata può essere un utente Azure AD o un'entità servizio, ad esempio un'applicazione Azure AD registrata.

- L' **attivazione del supporto di identità gestite nell'app per la logica** registra l'app per la logica con Azure ad e fornisce un ID oggetto. Usare l'ID oggetto in Sentinel di Azure per assegnare l'app per la logica con un ruolo di controllo degli accessi in base al ruolo di Azure. 

Per altre informazioni, vedere:

- [Autenticazione con identità gestita in app per la logica di Azure](/azure/logic-apps/create-managed-service-identity)
- [Documentazione del connettore app per la logica di Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Ottimizzazione della regola migliorata con i grafici di anteprima della regola di analisi (anteprima pubblica)

Azure Sentinel consente ora di ottimizzare meglio le regole di analisi, in modo da aumentare la precisione e ridurre il rumore.

Dopo aver modificato una regola di analisi nella scheda **impostare la logica della regola** , trovare l'area **simulazione risultati** a destra. 

Selezionare **test con dati correnti** per fare in modo che Sentinel di Azure esegua una simulazione delle ultime 50 esecuzioni della regola di analisi. Viene generato un grafico per mostrare il numero medio di avvisi generati dalla regola, in base ai dati degli eventi non elaborati valutati. 

Per altre informazioni, vedere [definire la logica di query della regola e configurare le impostazioni](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dicembre 2020

- [80 nuove query di caccia predefinite](#80-new-built-in-hunting-queries)
- [Miglioramenti di Log Analytics Agent](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nuove query di caccia predefinite
 
Le query di ricerca predefinite di Azure Sentinel consentono agli analisti SOC di ridurre i gap nella copertura di rilevamento corrente e di accendere nuovi lead di caccia.

Questo aggiornamento per il servizio Sentinel di Azure include nuove query di caccia che forniscono la copertura tra la matrice del Framework&CK:

- **Raccolta**
- **Comando e controllo**
- **Accesso alle credenziali**
- **Individuazione**
- **Esecuzione**
- **Esfiltrazione**
- **Impatto**
- **Accesso iniziale**
- **Persistenza**
- **Escalation dei privilegi**

Le query di ricerca aggiunte sono progettate per facilitare l'individuazione di attività sospette nell'ambiente in uso. Sebbene possano restituire attività legittime e attività potenzialmente dannose, possono essere utili per guidare la caccia. 

Se, dopo l'esecuzione di queste query, si è certi dei risultati, è possibile convertirli in regole di analisi o aggiungere risultati di caccia a eventi imprevisti nuovi o esistenti.

Tutte le query aggiunte sono disponibili tramite la pagina di ricerca di Azure Sentinel. Per altre informazioni, vedere [Hunt for Threats with Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Miglioramenti di Log Analytics Agent

Gli utenti di Sentinel di Azure traggono vantaggio dai miglioramenti Log Analytics agenti seguenti:

- **Supporto per più sistemi operativi**, tra cui CentOS 8, RedHat 8 e SUSE Linux 15.
- **Supporto per Python 3** , oltre a Python 2

Azure Sentinel usa l'agente di Log Analytics per inviare eventi all'area di lavoro, inclusi eventi di sicurezza di Windows, eventi syslog, log CEF e altro ancora.

> [!NOTE]
> L'agente di Log Analytics viene talvolta definito agente OMS o Microsoft Monitoring Agent (MMA). 
> 

Per ulteriori informazioni, vedere la [documentazione di log Analytics](/azure/azure-monitor/platform/log-analytics-agent) e le [Note sulla versione di log Analytics Agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembre 2020

- [Monitorare i PlayBook delle app per la logica in Sentinel di Azure](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Connettore Microsoft 365 Defender (anteprima pubblica)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Monitorare i PlayBook delle app per la logica in Sentinel di Azure

Azure Sentinel ora si integra con le [app log di Azure](/azure/logic-apps/), un servizio cloud che consente di pianificare, automatizzare e orchestrare attività, processi aziendali e flussi di lavoro.

Usare un'app per la logica di Azure in Sentinel di Azure come PlayBook, che può essere richiamato automaticamente quando viene creato un evento imprevisto o quando si esegue la valutazione e si lavora con gli eventi imprevisti. 

Per fornire informazioni dettagliate sull'integrità, sulle prestazioni e sull'utilizzo dei PlayBook, inclusi quelli aggiunti con le app per la logica di Azure, è stata aggiunta una [cartella di lavoro di Azure](/azure/azure-monitor/platform/workbooks-overview) denominata **Playbooks Health Monitoring**. 

Usare la cartella di lavoro di **monitoraggio dello stato dei PlayBook** per monitorare l'integrità dei PlayBook o cercare anomalie nella quantità di esecuzioni riuscite o non riuscite. 

La cartella di lavoro di **monitoraggio dell'integrità dei PlayBook** è ora disponibile nella raccolta di modelli di Sentinel di Azure:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Cartella di lavoro di monitoraggio dello stato di PlayBook di esempio":::

Per altre informazioni, vedere:

- [Documentazione di app per la logica](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Documentazione di Monitoraggio di Azure](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Connettore Microsoft 365 Defender (anteprima pubblica)
 
Il connettore Microsoft 365 Defender per Azure Sentinel consente di eseguire lo streaming di log di caccia avanzati, ovvero un tipo di dati di eventi non elaborati, da Microsoft 365 Defender ad Azure Sentinel. 

Con l'integrazione di [Microsoft Defender for endpoint (MDATP)](/windows/security/threat-protection/) nel [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) Security Umbrella, è ora possibile raccogliere gli eventi di caccia avanzati di Microsoft Defender per l'endpoint usando il connettore Microsoft 365 Defender e trasmetterli direttamente nelle nuove tabelle create per lo scopo nell'area di lavoro di Sentinel di Azure. 

Le tabelle di Azure Sentinel sono basate sullo stesso schema usato nel portale di Microsoft 365 Defender e offrono l'accesso completo al set completo di log di caccia avanzati. 

Per altre informazioni, vedere [connettere i dati da Microsoft 365 Defender ad Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender era noto in precedenza come Microsoft Threat Protection o MTP. Microsoft Defender for endpoint era noto in precedenza come Microsoft Defender Advanced Threat Protection o MDATP.
> 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Sentinella di Azure a bordo](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Ottenere visibilità sugli avvisi](quickstart-get-visibility.md)
