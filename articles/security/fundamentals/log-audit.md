---
title: Registrazione e controllo della sicurezza di Azure | Microsoft Docs
description: Informazioni sui log disponibili in Azure e sulle informazioni dettagliate sulla sicurezza che è possibile ottenere.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: 0d85cf6ae501a7d50f20e48543e361149f4b57d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580547"
---
# <a name="azure-security-logging-and-auditing"></a>Registrazione e controllo di sicurezza di Azure

Azure offre un'ampia gamma di opzioni di controllo e registrazione della sicurezza configurabili che consentono di identificare eventuali lacune nei criteri e nei meccanismi di sicurezza. Questo articolo offre informazioni per la generazione, la raccolta e l'analisi dei log di protezione di servizi ospitati in Azure.

> [!Note]
> Alcune indicazioni incluse in questo articolo potrebbero comportare un maggiore utilizzo delle risorse di dati, rete o calcolo e un aumento dei costi di licenza o di sottoscrizione.

## <a name="types-of-logs-in-azure"></a>Tipi di log in Azure

Le applicazioni cloud sono complesse e hanno molte parti mobili. La registrazione dei dati può fornire informazioni approfondite sulle applicazioni e aiutarti a:

- Risolvere i problemi precedenti o impedire potenziali
- Migliorare le prestazioni o la gestibilità delle applicazioni
- Automatizzare le azioni che richiederebbero altrimenti l'intervento manuale

I log di Azure sono suddivisi nei tipi seguenti:
* **Log di gestione/controllo**, che offrono informazioni sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager. Per altre informazioni, vedere la [Log attività di Azure](../../azure-monitor/essentials/platform-logs-overview.md).

* **Log del piano dati**, che offrono informazioni sugli eventi generati durante l'utilizzo di una risorsa di Azure. Sono esempi di questo tipo il registro eventi di sistema di Windows, il log di sicurezza, il log applicazioni di una macchina virtuale e i [log di diagnostica](../../azure-monitor/essentials/platform-logs-overview.md) configurati tramite Monitoraggio di Azure.

* **Eventi elaborati**, che offrono informazioni sugli eventi o avvisi analizzati dopo essere stati elaborati per conto dell'utente. Esempi di questo tipo sono i brevi [avvisi emessi dal Centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md) dopo aver eseguito l'[elaborazione e l'analisi della sottoscrizione](../../security-center/security-center-introduction.md).

La tabella seguente elenca i più importanti tipi di log disponibili in Azure.

| Categoria di log | Tipo di log | Utilizzo | Integrazione |
| ------------ | -------- | ------ | ----------- |
|[Log attività](../../azure-monitor/essentials/platform-logs-overview.md)|Gli eventi del piano di controllo sulle risorse di Azure Resource Manager|    Offrono informazioni dettagliate sulle operazioni eseguite sulle risorse nella sottoscrizione.|    API REST e [Monitoraggio di Azure](../../azure-monitor/essentials/platform-logs-overview.md)|
|[Log delle risorse di Azure](../../azure-monitor/essentials/platform-logs-overview.md)|Dati frequenti sul funzionamento delle risorse di Azure Resource Manager nella sottoscrizione| Offrono informazioni dettagliate sulle operazioni eseguite dalla risorsa stessa.| Monitoraggio di Azure|
|[Creazione di report Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Log e report | Segnalano attività di accesso dell'utente e informazioni sulle attività di sistema riguardo alla gestione di utenti e gruppi.|[API Graph](../../active-directory/develop/microsoft-graph-intro.md)|
|[Macchine virtuali e servizi cloud](../../azure-monitor/vm/quick-collect-azurevm.md)|Servizio Registro eventi di Windows e Syslog Linux| Acquisisce i dati di sistema e i dati di registrazione nelle macchine virtuali e li trasferisce all'account di archiviazione desiderato.|   Windows tramite [WAD](../../azure-monitor/agents/diagnostics-extension-overview.md) (archiviazione di Diagnostica di Windows Azure) e Linux in Monitoraggio di Azure|
|[Analisi archiviazione di Azure](/rest/api/storageservices/fileservices/storage-analytics)|Esegue la registrazione di archiviazione e offre i dati delle metriche per un account di archiviazione|Offre informazioni dettagliate per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione.| API REST o [libreria client](/dotnet/api/overview/azure/storage)|
|[Log del flusso del gruppo di sicurezza di rete (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Formato JSON, mostra i flussi in ingresso e in uscita in base a ciascuna regola|Visualizza le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insights](../../azure-monitor/app/app-insights-overview.md)|Log, eccezioni e diagnostica personalizzata|  Offre un servizio di monitoraggio delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme.| API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Dati di elaborazione/avvisi di sicurezza](../../security-center/security-center-introduction.md)|   Avvisi del Centro sicurezza di Azure, log di monitoraggio di Azure avvisi|    Offre informazioni e avvisi sulla sicurezza.|  API REST, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integrazione dei log con i sistemi SIEM locali
L' [integrazione degli avvisi del Centro sicurezza](../../security-center/security-center-partner-integration.md) illustra come sincronizzare gli avvisi del Centro sicurezza, gli eventi di sicurezza delle macchine virtuali raccolti dai log di diagnostica di Azure e i log di controllo di Azure con i log di monitoraggio di Azure o la soluzione Siem.

## <a name="next-steps"></a>Passaggi successivi

- [Controllo e registrazione](management-monitoring-overview.md): proteggere i dati mantenendo la visibilità e rispondendo rapidamente agli avvisi di sicurezza tempestivi.

- [Raccolta di log di registrazione e controllo della sicurezza all'interno di Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): applicare queste impostazioni per verificare che le istanze di Azure raccolgano i log di sicurezza e controllo corretti.

- [Configurare le impostazioni di controllo per una raccolta siti](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): gli amministratori di raccolte siti possono recuperare la cronologia delle azioni di singoli utenti e di quelle eseguite durante un intervallo di date specifico.

- [Eseguire una ricerca nel log di controllo nel centro sicurezza Microsoft 365](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance): usare il centro sicurezza Microsoft 365 per eseguire ricerche nel log di controllo unificato e visualizzare l'attività di utenti e amministratori nell'organizzazione.