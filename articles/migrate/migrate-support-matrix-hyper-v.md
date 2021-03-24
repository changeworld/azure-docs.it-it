---
title: Supporto per la valutazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la valutazione di Hyper-V con Azure Migrate individuazione e valutazione
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870790"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matrice di supporto per la valutazione di Hyper-V

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si individuano e valutano i server locali in esecuzione in un ambiente Hyper-V per la migrazione ad Azure, usando lo strumento [Azure migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Se si vuole eseguire la migrazione di server in esecuzione in Hyper-V in Azure, esaminare la [matrice di supporto](migrate-support-matrix-hyper-v-migration.md)per la migrazione.

Per configurare l'individuazione e la valutazione dei server in esecuzione in Hyper-V, è necessario creare un progetto e aggiungere lo strumento Azure Migrate: individuazione e valutazione al progetto. Dopo avere aggiunto lo strumento, distribuire [l'appliance di Azure Migrate](migrate-appliance.md). L'appliance individua continuamente i server locali e invia i metadati del server e i dati sulle prestazioni in Azure. Al termine dell'individuazione, è possibile raccogliere i server individuati in gruppi ed eseguire una valutazione per un gruppo.

## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 server in un singolo [progetto](migrate-support-matrix.md#project).
**Limiti di progetto** | In una sottoscrizione di Azure è possibile creare più progetti. Oltre ai server in Hyper-V, un progetto può includere server in VMware e server fisici, fino ai limiti di valutazione per ciascuno di essi.
**Individuazione** | L'appliance Azure Migrate è in grado di individuare fino a 5000 server in esecuzione in Hyper-V.<br/><br/> Il dispositivo può connettersi a un massimo di 300 host Hyper-V.
**Valutazione** | È possibile aggiungere fino a 35.000 server in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 server in una singola valutazione per un gruppo.

[Altre informazioni](concepts-assessment-calculation.md) sulle valutazioni.

## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**
| :-------------------       | :------------------- |
| **Host Hyper-V**       | L'host Hyper-V può essere autonomo o distribuito in un cluster.<br/><br/> L'host Hyper-V può eseguire Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2. Sono supportate anche le installazioni dei componenti di base del server di questi sistemi operativi. <br/>Non è possibile valutare i server che si trovano in host Hyper-V che eseguono Windows Server 2012.
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. <br/> Se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'account utente a questi gruppi: utenti di gestione remota, amministratori di Hyper-V e performance monitor. |
| **Comunicazione remota di PowerShell**   | La [comunicazione remota di PowerShell](/powershell/module/microsoft.powershell.core/enable-psremoting) deve essere abilitata in ogni host Hyper-V. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o sono presenti più server con gli stessi identificatori Server) e si individuano sia il server originale che quello replicato con Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |

## <a name="server-requirements"></a>Requisiti del server

| **Supporto**                  | **Dettagli**
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi possono essere valutati per la migrazione.  |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione nei server valutati. |
| **Archiviazione** | Disco locale, DAS, JBOD, spazi di archiviazione, CSV, SMB. Sono supportate le archiviazioni host Hyper-V in cui sono archiviati i dischi rigidi virtuali/VHDX. <br/> Sono supportati i controller virtuali IDE e SCSI|

## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Per l'individuazione e la valutazione, Azure Migrate usa l'[appliance di Azure Migrate](migrate-appliance.md). È possibile distribuire l'appliance usando un disco rigido virtuale Hyper-V compresso che è possibile scaricare dal portale o usando uno [script di PowerShell](deploy-appliance-script.md).

- Informazioni sui [requisiti di appliance](migrate-appliance.md#appliance---hyper-v) per Hyper-V.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e [per enti pubblici](migrate-appliance.md#government-cloud-urls).
- In Azure per enti pubblici è necessario distribuire l'appliance [tramite lo script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire la connessione dal desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione dell'appliance tramite l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Host/cluster Hyper-V** | Connessione in ingresso sulla porta WinRM 5985 (HTTP) o 5986 (HTTPS) per eseguire il pull dei dati relativi ai metadati e alle prestazioni per i server in Hyper-V, usando una sessione di Common Information Model (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti dell'analisi delle dipendenze basata su agente

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra i server locali di cui si vuole eseguire la valutazione e la migrazione ad Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente. Hyper-V supporta attualmente solo la visualizzazione delle dipendenze basate su agenti.

**Requisito** | **Dettagli**
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto, con lo strumento Azure Migrate: individuazione e valutazione aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i server locali.<br/><br/> [Informazioni](create-manage-projects.md) su come creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere Azure migrate: strumento di individuazione e valutazione a un progetto esistente.<br/> Informazioni su come configurare l'appliance per l'individuazione e la valutazione dei [server in Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [Mapping dei servizi](../azure-monitor/vm/service-map.md) in [Log di Monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Si associa un'area di lavoro Log Analytics nuova o esistente a un progetto di. Non è possibile modificare l'area di lavoro per un progetto dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sud-orientale o Europa occidentale. Non è possibile associare a un progetto aree di lavoro di altre regioni.<br/><br/> L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi è supportata](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave di migrazione progetto e con il nome del progetto.
**Agenti obbligatori** | In ogni server che si desidera analizzare installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Se i server locali non sono connessi a Internet, è necessario scaricare e installare Log Analytics gateway.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto.<br/><br/> Azure Migrate supporta aree di lavoro nelle regioni Stati Uniti orientali, Asia sud-orientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) è supportata.<br/><br/> Non è possibile modificare l'area di lavoro per un progetto dopo che è stata aggiunta.
**Costi** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> Se si usa una soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata verranno applicati gli [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics.<br/><br/> Quando il progetto viene eliminato, l'area di lavoro non viene eliminata insieme a essa. Dopo l'eliminazione del progetto, l'uso di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro Log Analytics/<br/><br/>Se sono presenti progetti creati prima che Azure Migrate fosse generalmente disponibile (disponibilità generale: 28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per Mapping dei servizi. Per garantire che l'addebito avvenga solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, si utilizzano l'ID e la chiave forniti dal progetto.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarla manualmente](../azure-monitor/logs/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i server non sono connessi a Internet, è necessario installare il gateway Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione dei server in esecuzione in Hyper-V](./tutorial-discover-hyper-v.md).