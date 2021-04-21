---
title: Panoramica dei server con abilitazione di Azure Arc
description: Informazioni su come usare i server con abilitazione di Azure Arc per gestire server ospitati all'esterno di Azure come una risorsa di Azure.
keywords: automazione di azure, DSC, powershell, configurazione dello stato desiderato, gestione aggiornamenti, rilevamento modifiche, inventario, runbook, python, grafico, ibrido
ms.date: 04/21/2021
ms.topic: overview
ms.openlocfilehash: 324f6cc29bd9e4eca1a20413032c213c2618a11e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831997"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Che cosa sono i server con abilitazione di Azure Arc?

Azure Arc server abilitati consente di gestire i server fisici Windows  e Linux e le macchine virtuali ospitate all'esterno di Azure, nella rete aziendale o in un altro provider di servizi cloud. Questa esperienza di gestione è progettata per essere coerente con la modalità di gestione delle macchine virtuali native di Azure. Quando una macchina virtuale ibrida viene connessa ad Azure, diventa una macchina virtuale connessa e viene considerata come una risorsa in Azure. Ogni macchina virtuale connessa ha un ID risorsa, è inclusa in un gruppo di risorse e usufruisce dei vantaggi derivanti dai costrutti di Azure standard, ad esempio Criteri di Azure e applicazione di tag. I provider di servizi che gestiscono l'infrastruttura locale di un cliente possono gestire le proprie macchine virtuali ibride in più ambienti di clienti, proprio come avviene oggi con le risorse native di Azure, usando [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) con Azure Arc.

Per offrire questa esperienza con le macchine virtuali ibride ospitate all'esterno di Azure, è necessario installare l'agente Azure Connected Machine in ogni macchina virtuale che si prevede di connettere ad Azure. Questo agente non fornisce altre funzionalità e non sostituisce l'[agente di Azure Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vuole monitorare in modo proattivo il sistema operativo e i carichi di lavoro in esecuzione nella macchina virtuale, gestirla con runbook di automazione o soluzioni come Gestione aggiornamenti o usare altri servizi di Azure, come il [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Scenari supportati

Quando si connette la macchina virtuale a server con abilitazione di Azure Arc, è possibile eseguire le attività di gestione della configurazione e di monitoraggio seguenti:

- Assegnare [configurazioni guest di Criteri di Azure](../../governance/policy/concepts/guest-configuration.md) usando la stessa esperienza dell'assegnazione dei criteri per le macchine virtuali di Azure. Oggi la maggior parte dei criteri di Configurazione guest non applicano configurazioni, ma si limitano a controllare le impostazioni all'interno del computer. Per informazioni sul costo associato all'uso dei criteri di Configurazione guest di Criteri di Azure con server con abilitazione di Arc, vedere la [guida sui prezzi](https://azure.microsoft.com/pricing/details/azure-policy/) di Criteri di Azure.

- Report sulle modifiche di configurazione relative a software installato, servizi Microsoft, Registro di sistema e file di Windows e daemon Linux nei server monitorati tramite Automazione di Azure [Rilevamento modifiche e inventario](../../automation/change-tracking/overview.md) e monitoraggio dell'integrità dei file di [Centro sicurezza di Azure](../../security-center/security-center-file-integrity-monitoring.md), per i server abilitati con Azure Defender per [i server](../../security-center/defender-for-servers-introduction.md).

- Monitorare le prestazioni del sistema operativo guest del computer connesso e individuare i componenti dell'applicazione per monitorarne i processi e le dipendenze con altre risorse comunicate dall'applicazione usando informazioni [dettagliate sulle macchine virtuali.](../../azure-monitor/vm/vminsights-overview.md)

- Semplificare la distribuzione usando altri servizi di Azure, ad esempio Automazione di Azure [State Configuration](../../automation/automation-dsc-overview.md) e l'area di lavoro Log Analytics di Monitoraggio di Azure, usando le estensioni di macchina virtuale di [Azure](manage-vm-extensions.md) supportate per il computer Windows o Linux non Azure. È inclusa l'esecuzione della configurazione post-distribuzione o l'installazione del software tramite l'estensione Script personalizzato.

- È possibile usare [Gestione aggiornamenti](../../automation/update-management/overview.md) di Automazione di Azure per gestire gli aggiornamenti del sistema operativo per i server Windows e Linux

    > [!NOTE]
    > Al momento, l'abilitazione di Gestione aggiornamenti direttamente da un server con abilitazione di Arc non è supportata. Per informazioni sui requisiti e su come eseguire l'abilitazione per il server, vedere [Abilitare Gestione aggiornamenti dal proprio account di Automazione](../../automation/update-management/enable-from-automation-account.md).

- Includere i server non di Azure per il rilevamento delle minacce e monitorare in modo proattivo le potenziali minacce per la sicurezza usando [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).

I dati di log raccolti e archiviati in un'area di lavoro Log Analytics dalla macchina virtuale ibrida includono ora proprietà specifiche della macchina virtuale, ad esempio un ID risorsa, che possono essere usate per supportare l'accesso ai log di tipo [risorsa-contesto](../../azure-monitor/logs/design-logs-deployment.md#access-mode).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Aree supportate

Per un elenco definitivo delle aree supportate con i server con abilitazione di Azure Arc, vedere la pagina [Prodotti Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

Nella maggior parte dei casi, la posizione selezionata durante la creazione dello script di installazione deve essere l'area di Azure geograficamente più vicina alla posizione del computer. I dati inattivi vengono archiviati all'interno dell'area geografica di Azure contenente l'area specificata, il che potrebbe influire anche sulla scelta dell'area in caso di requisiti di residenza dei dati. Se l'area di Azure a cui è connesso il computer è interessata da un'interruzione del servizio, il computer connesso non sarà interessato, ma le operazioni di gestione che usano Azure potrebbero non venire completate. Se si verifica un'interruzione a livello di area e se si dispone di più località che supportano un servizio con ridondanza geografica, è meglio connettere i computer in ogni posizione a un'area di Azure diversa.

Le informazioni dei metadati seguenti sul computer connesso vengono raccolte e archiviate nell'area in cui è configurata la risorsa computer Azure Arc:

- Nome e versione del sistema operativo
- Nome computer
- Nome di dominio completo (FQDN) del computer
- Versione dell'agente Connected Machine

Se ad esempio il computer è registrato con Azure Arc nell'area Stati Uniti orientali, questi dati vengono archiviati nell'area Stati Uniti.

### <a name="supported-environments"></a>Ambienti supportati

I server abilitati per Arc supportano la gestione di server fisici e macchine virtuali ospitate *all'esterno* di Azure. Per informazioni dettagliate specifiche su quali ambienti cloud ibridi che ospitano macchine virtuali, vedere [Prerequisiti dell'agente Connected Machine.](agent-overview.md#supported-environments)

> [!NOTE]
> I server con abilitazione di Arc non sono progettati o supportati per consentire la gestione delle macchine virtuali in esecuzione in Azure.

### <a name="agent-status"></a>Stato dell'agente

L'agente Connected Machine invia un messaggio regolare di tipo heartbeat al servizio ogni 5 minuti. Se il servizio smette di ricevere questi messaggi di tipo heartbeat da un computer, quest'ultimo viene considerato offline e lo stato verrà automaticamente modificato in **Disconnesso** nel portale in un arco di tempo compreso tra 15 e 30 minuti. Alla ricezione di un messaggio successivo di tipo heartbeat dall'agente Connected Machine, il relativo stato verrà automaticamente modificato in **Connesso**.

## <a name="next-steps"></a>Passaggi successivi

Prima di valutare o abilitare i server con abilitazione di Arc tra più macchine virtuali ibride, vedere [Panoramica dell'agente Connected Machine](agent-overview.md) per informazioni sui requisiti, i dettagli tecnici sull'agente e i metodi di distribuzione.