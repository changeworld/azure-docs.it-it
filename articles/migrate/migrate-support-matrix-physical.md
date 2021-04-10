---
title: Supporto per l'individuazione e la valutazione fisica in Azure Migrate
description: Informazioni sul supporto per l'individuazione e la valutazione fisiche con Azure Migrate individuazione e valutazione
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: b62160861f686c6ea5a8ebfd03d904da2ad5d80a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869447"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>Matrice di supporto per l'individuazione e la valutazione dei server fisici 

Questo articolo riepiloga i prerequisiti e i requisiti di supporto quando si valutano i server fisici per la migrazione ad Azure, usando lo strumento [Azure migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Per eseguire la migrazione di server fisici ad Azure, esaminare la [matrice di supporto](migrate-support-matrix-physical-migration.md)per la migrazione.

Per valutare i server fisici, creare un progetto e aggiungere lo strumento Azure Migrate: individuazione e valutazione al progetto. Dopo avere aggiunto lo strumento, distribuire [l'appliance di Azure Migrate](migrate-appliance.md). L'appliance individua continuamente i server locali e invia i metadati dei server e i dati sulle prestazioni in Azure. Al termine dell'individuazione, è possibile raccogliere i server individuati in gruppi ed eseguire una valutazione per un gruppo.

## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione** | È possibile individuare e valutare fino a 35.000 di server fisici in un singolo [progetto](migrate-support-matrix.md#project).
**Limiti di progetto** | In una sottoscrizione di Azure è possibile creare più progetti. Oltre ai server fisici, un progetto può includere server in VMware e in Hyper-V, fino ai limiti di valutazione per ciascuno di essi.
**Individuazione** | L'appliance Azure Migrate è in grado di individuare fino a 1000 di server fisici.
**Valutazione** | È possibile aggiungere fino a 35.000 server in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 server in una singola valutazione.

[Altre informazioni](concepts-assessment-calculation.md) sulle valutazioni.

## <a name="physical-server-requirements"></a>Requisiti per i server fisici

**Distribuzione server fisico:** Il server fisico può essere autonomo o distribuito in un cluster.

**Sistema operativo:** Tutti i sistemi operativi Windows e Linux possono essere valutati per la migrazione.

**Autorizzazioni:**

- Per i server Windows, usare un account di dominio per i server aggiunti a un dominio e un account locale per i server non aggiunti a un dominio. L'account utente deve essere aggiunto a questi gruppi: Utenti Gestione remota, Performance Monitor Users e Performance Log Users.
- Per i server Linux, è necessario un account radice nei server Linux che si desidera individuare. In alternativa, è possibile impostare un account non radice con le funzionalità necessarie usando i comandi seguenti:

**Comando** | **Scopo**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(if /usr/sbin/fdisk is not present)_ | Per raccogliere i dati di configurazione del disco
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Per raccogliere dati sulle prestazioni del disco
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Per raccogliere il numero di serie del BIOS
chmod a+r /sys/class/dmi/id/product_uuid | Per raccogliere il GUID BIOS

## <a name="azure-migrate-appliance-requirements"></a>Requisiti dell'appliance di Azure Migrate

Per l'individuazione e la valutazione, Azure Migrate usa l'[appliance di Azure Migrate](migrate-appliance.md). L'appliance per i server fisici può essere eseguita in una macchina virtuale o in un server fisico.

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---physical) per i server fisici.
- Informazioni sugli URL a cui l'appliance deve accedere nei cloud [pubblico](migrate-appliance.md#public-cloud-urls) e [per enti pubblici](migrate-appliance.md#government-cloud-urls).
- È possibile configurare l'appliance usando uno [script di PowerShell](how-to-set-up-appliance-physical.md) che è possibile scaricare dal portale di Azure.
In Azure per enti pubblici distribuire l'appliance [usando questo script](deploy-appliance-script-government.md).

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389, per consentire le connessioni Desktop remoto al dispositivo.<br/><br/> Connessioni in ingresso sulla porta 44368, per accedere in remoto all'app di gestione Appliance usando l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Connessioni in uscita sulle porte 443 (HTTPS) per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Server fisici** | **Windows:** Connessione in ingresso sulla porta WinRM 5985 (HTTP) o 5986 (HTTPS) per il pull dei metadati di configurazione e prestazioni dai server Windows. <br/><br/> **Linux:**  Connessioni in ingresso sulla porta 22 (TCP), per eseguire il pull dei metadati di configurazione e delle prestazioni dai server Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Requisiti dell'analisi delle dipendenze basata su agente

L' [analisi](concepts-dependency-visualization.md) delle dipendenze consente di identificare le dipendenze tra i server locali di cui si vuole eseguire la valutazione e la migrazione ad Azure. Nella tabella sono riepilogati i requisiti per la configurazione dell'analisi delle dipendenze basata su agente. Attualmente solo l'analisi delle dipendenze basata su agenti è supportata per i server fisici.

**Requisito** | **Dettagli**
--- | ---
**Prima della distribuzione** | È necessario disporre di un progetto, con lo strumento Azure Migrate: individuazione e valutazione aggiunto al progetto.<br/><br/>  La visualizzazione delle dipendenze viene distribuita dopo la configurazione di un appliance Azure Migrate per individuare i server locali<br/><br/> [Informazioni](create-manage-projects.md) su come creare un progetto per la prima volta.<br/> [Informazioni](how-to-assess.md) su come aggiungere uno strumento di valutazione a un progetto esistente.<br/> Informazioni su come configurare l'appliance Azure Migrate per la valutazione di server [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) o fisici.
**Azure Government** | La visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.
**Log Analytics** | Azure Migrate usa la soluzione [Mapping dei servizi](../azure-monitor/vm/service-map.md) in [Log di Monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md) per la visualizzazione delle dipendenze.<br/><br/> Si associa un'area di lavoro Log Analytics nuova o esistente a un progetto di. Non è possibile modificare l'area di lavoro per un progetto dopo che è stata aggiunta. <br/><br/> L'area di lavoro deve trovarsi nella stessa sottoscrizione del progetto.<br/><br/> L'area di lavoro deve trovarsi nelle aree Stati Uniti orientali, Asia sud-orientale o Europa occidentale. Non è possibile associare a un progetto aree di lavoro di altre regioni.<br/><br/> L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi è supportata](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave di migrazione progetto e con il nome del progetto.
**Agenti obbligatori** | In ogni server che si desidera analizzare installare gli agenti seguenti:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Dependency Agent](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Se i server locali non sono connessi a Internet, è necessario scaricare e installare Log Analytics gateway.<br/><br/> Altre informazioni sull'installazione di [Dependency Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**area di lavoro Log Analytics** | L'area di lavoro deve trovarsi nella stessa sottoscrizione di un progetto.<br/><br/> Azure Migrate supporta aree di lavoro nelle regioni Stati Uniti orientali, Asia sud-orientale ed Europa occidentale.<br/><br/>  L'area di lavoro deve trovarsi in una regione in cui la soluzione [Mapping dei servizi](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) è supportata.<br/><br/> Non è possibile modificare l'area di lavoro per un progetto dopo che è stata aggiunta.
**Costi** | La soluzione Mapping dei servizi non comporta alcun addebito per i primi 180 giorni (dal giorno in cui si associa l'area di lavoro Log Analytics al progetto)/<br/><br/> Dopo 180 giorni verranno applicati gli addebiti standard di Log Analytics.<br/><br/> Se si usa una soluzione diversa da Mapping dei servizi nell'area di lavoro Log Analytics associata verranno applicati gli [addebiti standard](https://azure.microsoft.com/pricing/details/log-analytics/) di Log Analytics.<br/><br/> Quando il progetto viene eliminato, l'area di lavoro non viene eliminata insieme a essa. Dopo l'eliminazione del progetto, l'uso di Mapping dei servizi non sarà gratuito e ogni nodo verrà addebitato in base al livello a pagamento dell'area di lavoro Log Analytics/<br/><br/>Se sono presenti progetti creati prima che Azure Migrate fosse generalmente disponibile (disponibilità generale: 28 febbraio 2018), è possibile che vengano addebitati costi aggiuntivi per Mapping dei servizi. Per garantire che l'addebito avvenga solo dopo 180 giorni, è consigliabile creare un nuovo progetto, perché le aree di lavoro esistenti prima della disponibilità generale sono ancora addebitabili.
**Gestione** | Quando si registrano gli agenti nell'area di lavoro, si utilizzano l'ID e la chiave forniti dal progetto.<br/><br/> È possibile usare l'area di lavoro Log Analytics all'esterno di Azure Migrate.<br/><br/> Se si elimina il progetto associato, l'area di lavoro non viene eliminata automaticamente. [Eliminarla manualmente](../azure-monitor/logs/manage-access.md).<br/><br/> Non eliminare l'area di lavoro creata da Azure Migrate, a meno che non si elimini il progetto. In caso contrario, la funzionalità di visualizzazione delle dipendenze non funzionerà come previsto.
**Connettività Internet** | Se i server non sono connessi a Internet, è necessario installare il gateway Log Analytics.
**Azure Government** | L'analisi delle dipendenze basata su agente non è supportata.

## <a name="next-steps"></a>Passaggi successivi

[Prepararsi per l'individuazione fisica e la valutazione](./tutorial-discover-physical.md).