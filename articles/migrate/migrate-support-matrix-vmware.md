---
title: Supporto per la valutazione VMware in Azure Migrate
description: Informazioni sul supporto della valutazione per i server in esecuzione nell'ambiente VMware con Azure Migrate individuazione e valutazione
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 4d51fc13e3587c21a7340b35db10d3cf36ab74b5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557548"
---
# <a name="support-matrix-for-vmware-assessment"></a>Matrice di supporto per la valutazione di VMware 

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si individuano e valutano i server in esecuzione nell'ambiente VMware per la migrazione ad Azure, usando lo strumento [Azure migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Per valutare i server, è necessario creare un progetto che aggiunga lo strumento Azure Migrate: individuazione e valutazione al progetto. Dopo avere aggiunto lo strumento, distribuire l'appliance di Azure Migrate. L'appliance individua continuamente i server locali e invia i metadati di configurazione e prestazioni ad Azure. Al termine dell'individuazione, è possibile raccogliere i server individuati in gruppi ed eseguire una valutazione per un gruppo.

Per eseguire la migrazione dei server VMware ad Azure, esaminare la [matrice di supporto](migrate-support-matrix-vmware-migration.md)per la migrazione.



## <a name="limitations"></a>Limitazioni

**Requisito** | **Dettagli**
--- | ---
**Limiti di progetto** | In una sottoscrizione di Azure è possibile creare più progetti.<br/><br/> È possibile individuare e valutare fino a 50.000 server dall'ambiente VMware in un singolo [progetto](migrate-support-matrix.md#project). Un progetto può includere anche server fisici e server dall'ambiente Hyper-V, fino ai limiti di valutazione.
**Individuazione** | L'appliance Azure Migrate è in grado di individuare fino a 10.000 server in una server vCenter.
**Valutazione** | È possibile aggiungere fino a 35.000 server in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 server in una singola valutazione.

[Altre informazioni](concepts-assessment-calculation.md) sulle valutazioni.


## <a name="vmware-requirements"></a>Requisiti di VMware

**VMware** | **Dettagli**
--- | ---
**Server vCenter** | I server che si desidera individuare e valutare devono essere gestiti da server vCenter versione 5,5, 6,0, 6,5, 6,7 o 7,0.<br/><br/> L'individuazione dei server fornendo i dettagli dell'host ESXi nell'appliance non è attualmente supportata.
**Autorizzazioni** | Azure Migrate: individuazione e valutazione necessitano di un account server vCenter di sola lettura per l'individuazione e la valutazione.<br/><br/> Se si vuole eseguire l'individuazione dell'inventario software e dell'analisi delle dipendenze senza agenti, l'account deve avere i privilegi abilitati per  >  **le operazioni Guest** delle macchine virtuali.

## <a name="server-requirements"></a>Requisiti del server
**VMware** | **Dettagli**
--- | ---
**Sistema operativo supportato** | Tutti i sistemi operativi Windows e Linux possono essere valutati per la migrazione.
**Archiviazione** | Sono supportati i dischi collegati ai controller SCSI, IDE e SATA.


## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Per l'individuazione e la valutazione, Azure Migrate usa l'[appliance di Azure Migrate](migrate-appliance.md). È possibile distribuire l'appliance come server nell'ambiente VMware usando un modello OVA, importato in server vCenter o usando uno [script di PowerShell](deploy-appliance-script.md).

- Informazioni sui [requisiti dell'appliance](migrate-appliance.md#appliance---vmware) per VMware.
- In Azure per enti pubblici è necessario distribuire l'appliance [tramite lo script](deploy-appliance-script-government.md).
- Esaminare gli URL necessari all'appliance per accedere ai cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls) .


## <a name="port-access-requirements"></a>Requisiti di accesso alle porte

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire la connessione dal desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione dell'appliance tramite l'URL: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connessioni in uscita sulla porta 443 (HTTPS) per inviare metadati di individuazione e prestazioni ad Azure Migrate.
**Server vCenter** | Connessioni in ingresso sulla porta TCP 443 per consentire all'appliance di raccogliere metadati di configurazione e prestazioni per le valutazioni. <br/><br/> Per impostazione predefinita, l'appliance si connette a vCenter sulla porta 443. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si configura l'individuazione.
**Host ESXi** | Se si vuole eseguire l' [individuazione dell'inventario software](how-to-discover-applications.md)o l' [analisi delle dipendenze senza agenti](concepts-dependency-visualization.md#agentless-analysis), l'appliance si connette agli host ESXi sulla porta TCP 443, per individuare l'inventario software e le dipendenze nei server.

## <a name="application-discovery-requirements"></a>Requisiti di individuazione delle applicazioni

Oltre a individuare i server, Azure Migrate: individuazione e valutazione consentono di individuare l'inventario software in esecuzione sui server. L'individuazione delle applicazioni consente di identificare e pianificare un percorso di migrazione personalizzato per i carichi di lavoro locali.

**Supporto** | **Dettagli**
--- | ---
**Server supportati** | Attualmente supportato solo per i server nell'ambiente VMware. È possibile eseguire l'individuazione delle applicazioni in un massimo di 10000 server, da ogni appliance Azure Migrate.
**Sistemi operativi** | Sono supportati i server che eseguono tutte le versioni di Windows e Linux.
**Requisiti della macchina virtuale** | Per eseguire l'individuazione dell'inventario software, è necessario che gli strumenti VMware siano installati e in esecuzione nei server. <br/><br/> La versione degli strumenti VMware deve essere successiva alla 10.2.0.<br/><br/> Per i server Windows deve essere installata la versione 2,0 o successiva di PowerShell.
**Individuazione** | L'individuazione delle applicazioni nei server viene eseguita dal server vCenter, usando gli strumenti VMware installati nei server. L'appliance raccoglie le informazioni sull'inventario software dal server vCenter, usando le API di vSphere. L'individuazione dell'applicazione è senza agente. Nessun agente è installato nel server e l'appliance non si connette direttamente ai server. WMI e SSH devono essere abilitati e disponibili rispettivamente nei server Windows e Linux.
**account utente server vCenter** | Per l'server vCenter account di sola lettura utilizzato per la valutazione, è necessario abilitare i privilegi per  >  **le operazioni Guest** delle macchine virtuali, in modo da interagire con i server per l'individuazione delle applicazioni.
**Accesso al server** | È possibile aggiungere più credenziali di dominio e non di dominio (Windows/Linux) in Gestione configurazione Appliance per l'individuazione delle applicazioni.<br/><br/> È necessario un account utente Guest per i server Windows e un account utente normale/normale (accesso non sudo) per tutti i server Linux.
**Accesso alla porta** | Il dispositivo di Azure Migrate deve essere in grado di connettersi alla porta TCP 443 in ESXi ospita server in cui si vuole eseguire l'individuazione delle applicazioni. Il server vCenter restituisce una connessione all'host ESXi, per scaricare il file contenente i dettagli dell'inventario software.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Requisiti per l'individuazione di istanze e database di SQL Server

L' [individuazione delle applicazioni](how-to-discover-applications.md) identifica le istanze di SQL Server. Usando queste informazioni, l'appliance tenta di connettersi alle rispettive istanze di SQL Server tramite l'autenticazione di Windows o le credenziali di autenticazione SQL Server fornite nell'appliance. Una volta stabilita la connessione, appliance raccoglie i dati di configurazione e delle prestazioni delle istanze di SQL Server e dei database. I dati di configurazione SQL Server vengono aggiornati ogni 24 ore e i dati sulle prestazioni vengono acquisiti ogni 30 secondi.

**Supporto** | **Dettagli**
--- | ---
**Server supportati** | Attualmente supportato solo per SQL Server nell'ambiente VMware. È possibile individuare fino a 300 istanze di SQL Server o 6000 database SQL, a seconda di quale sia il valore inferiore.
**Server Windows** | Sono supportati Windows Server 2008 e versioni successive.
**Server Linux** | Attualmente non supportato.
**Meccanismo di autenticazione** | Sono supportate sia l'autenticazione Windows che la SQL Server. È possibile fornire le credenziali di entrambi i tipi di autenticazione in Gestione configurazione Appliance.
**Accesso a SQL Server** | Azure Migrate richiede un account utente di Windows membro del ruolo del server sysadmin.
**Versioni di SQL Server** | Sono supportati SQL Server 2008 e versioni successive.
**Edizioni di SQL Server** | Sono supportate le edizioni Enterprise, standard, Developer ed Express.
**Configurazione SQL supportata** | È attualmente supportata l'individuazione di solo istanze di SQL Server autonome e database corrispondenti.<br/> L'identificazione del cluster di failover e Gruppi di disponibilità Always On non è supportata.
**Servizi SQL supportati** | È supportata solo SQL Server motore di database. <br/> L'individuazione di SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) e SQL Server Analysis Services (SSAS) non è supportata.

> [!Note]
> Azure Migrate crittografa la comunicazione tra le istanze Azure Migrate Appliance e SQL Server di origine (con la proprietà Crittografa connessione impostata su TRUE). Queste connessioni vengono crittografate con [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (impostato su true); il livello trasporto userà SSL per crittografare il canale e ignorare la catena di certificati per convalidare l'attendibilità. Il server appliance deve essere configurato in modo da [**considerare attendibile l'autorità radice del certificato**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Se al momento dell'avvio non è stato eseguito il provisioning di alcun certificato sul server, SQL Server genera un certificato autofirmato che viene utilizzato per crittografare i pacchetti di accesso. [**Scopri di più**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Requisiti di analisi delle dipendenze (senza agenti)

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra i server locali di cui si vuole eseguire la valutazione e la migrazione ad Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze senza agente.

**Supporto** | **Dettagli**
--- | --- 
**Server supportati** | Attualmente supportato solo per i server nell'ambiente VMware.
**Server Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bit).<br/>Microsoft Windows Server 2008 (32 bit).
**Server Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 e versioni successive.
**Requisiti del server** | È necessario che gli strumenti VMware (successivi a 10.2.0) siano installati e in esecuzione nei server che si desidera analizzare.<br/><br/> Per i server deve essere installata la versione 2,0 o successiva di PowerShell.
**Metodo di individuazione** |  Le informazioni sulle dipendenze tra i server vengono raccolte dalla server vCenter, usando gli strumenti VMware installati nel server. L'appliance raccoglie le informazioni dalla server vCenter, usando le API di vSphere. Nessun agente è installato nel server e l'appliance non si connette direttamente ai server. WMI e SSH devono essere abilitati e disponibili rispettivamente nei server Windows e Linux.
**account vCenter** | L'account di sola lettura utilizzato da Azure Migrate per la valutazione necessita dei privilegi abilitati per **le macchine virtuali > le operazioni Guest**.
**Autorizzazioni di Windows Server** |  Un account utente (locale o di dominio) con autorizzazioni amministrative sui server.
**Account Linux** | Account utente radice o un account con queste autorizzazioni per i file/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.<br/><br/> Configurare queste funzionalità usando i comandi seguenti: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Accesso alla porta** | Il dispositivo di Azure Migrate deve essere in grado di connettersi alla porta TCP 443 negli host ESXi che eseguono i server di cui si desidera individuare le dipendenze. Il server vCenter restituisce una connessione all'host ESXi, per scaricare il file contenente i dati di dipendenza.


## <a name="dependency-analysis-requirements-agent-based"></a>Requisiti dell'analisi delle dipendenze (basata su agenti)

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra i server locali di cui si vuole eseguire la valutazione e la migrazione ad Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente.

**Requisito** | **Dettagli** 
--- | --- 
**Prima della distribuzione** | È necessario disporre di un progetto, con lo strumento Azure Migrate: individuazione e valutazione aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo aver configurato un'appliance Azure Migrate per individuare i server locali.<br/><br/> [Informazioni](create-manage-projects.md) su come creare un progetto per la prima volta.<br/> [Informazioni su come](how-to-assess.md) aggiungere uno strumento di individuazione e valutazione a un progetto esistente.<br/> Informazioni su come configurare l'appliance Azure Migrate per la valutazione di server [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) o fisici.
**Server supportati** | Supportato per tutti i server nell'ambiente locale.
**Log Analytics** | Azure Migrate usa la soluzione [Mapping dei servizi](../azure-monitor/insights/service-map.md) in [Log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Si associa un'area di lavoro Log Analytics nuova o esistente a un progetto di. Non è possibile modificare l'area di lavoro per un progetto dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sud-orientale o Europa occidentale. Non è possibile associare a un progetto aree di lavoro di altre regioni.<br/><br/> L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi è supportata](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics, l'area di lavoro associata a Azure Migrate è contrassegnata con la chiave del progetto e il nome del progetto.
**Agenti obbligatori** | In ogni server che si desidera analizzare installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Se i server locali non sono connessi a Internet, è necessario scaricare e installare Log Analytics gateway.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto.<br/><br/> Azure Migrate supporta aree di lavoro nelle regioni Stati Uniti orientali, Asia sud-orientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) è supportata.<br/><br/> Non è possibile modificare l'area di lavoro per un progetto dopo che è stata aggiunta.
**Costi** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> Se si usa una soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata verranno applicati gli [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics.<br/><br/> Quando il progetto viene eliminato, l'area di lavoro non viene eliminata insieme a essa. Dopo l'eliminazione del progetto, l'uso di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro Log Analytics/<br/><br/>Se sono presenti progetti creati prima che Azure Migrate fosse generalmente disponibile (disponibilità generale: 28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per Mapping dei servizi. Per garantire che l'addebito avvenga solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, si utilizzano l'ID e la chiave forniti dal progetto.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarla manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i server non sono connessi a Internet, è necessario installare il gateway Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.


## <a name="next-steps"></a>Passaggi successivi

- [Rivedere](best-practices-assessment.md) le procedure consigliate per la creazione di valutazioni.
- [Preparare la valutazione VMware](./tutorial-discover-vmware.md).