---
title: Informazioni su Azure Migrate
description: Informazioni sul servizio Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 858f006de7425a9fa8bea25e356a148b877aa30d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040601"
---
# <a name="about-azure-migrate"></a>Informazioni su Azure Migrate

Questo articolo offre una rapida panoramica del servizio Azure Migrate.

Azure Migrate offre un hub centralizzato per la valutazione e la migrazione ad Azure dei server, dell'infrastruttura, delle applicazioni e dei dati locali. Fornisce le risorse seguenti:

- **Piattaforma di migrazione unificata**: un singolo portale per avviare, eseguire e tenere traccia della migrazione ad Azure.
- **Gamma di strumenti**: Vari strumenti per la valutazione e la migrazione. Gli strumenti Azure Migrate includono Azure Migrate: Valutazione server o Azure Migrate: Server Migration. Azure Migrate si integra con altri servizi e strumenti di Azure, oltre che con offerte di fornitori di software indipendenti (ISV).
- **Valutazione e migrazione**: nell'hub di Azure Migrate è possibile valutare ed eseguire la migrazione degli elementi seguenti:
    - **Server**: per valutare i server locali ed eseguirne la migrazione a macchine virtuali di Azure o alla soluzione Azure VMware (anteprima).
    - **Database**: per valutare i database locali ed eseguirne la migrazione al database SQL di Azure o a Istanza gestita di SQL.
    - **Applicazioni Web**: per valutare applicazioni Web locali ed eseguirne la migrazione al Servizio app di Azure con Azure App Service Migration Assistant.
    - **Desktop virtuali**: per valutare l'infrastruttura VDI (Virtual Desktop Infrastructure) locale ed eseguirne la migrazione a Desktop virtuale Windows in Azure.
    - **Data**: per eseguire la migrazione di grandi quantità di dati ad Azure in modo rapido e a costi contenuti con i prodotti Azure Data Box.

## <a name="integrated-tools"></a>Strumenti integrati

L'hub Azure Migrate include questi strumenti:

**Strumento** | **Valutazione e migrazione** | **Dettagli**
--- | --- | ---
**Azure Migrate: Valutazione server** | Valutazione dei server. | Consente di individuare e valutare le macchine virtuali VMware e Hyper-V locali e i server fisici in preparazione per la migrazione ad Azure.
**Azure Migrate: Migrazione server** | Migrazione dei server. | Consente di eseguire la migrazione ad Azure di macchine virtuali VMware e Hyper-V, server fisici, oltre ad altre macchine virtualizzate e VM del cloud pubblico.
**Data Migration Assistant** | Consente di valutare i database di SQL Server per la migrazione a database SQL di Azure, Istanza gestita di SQL di Azure o macchine virtuali di Azure che eseguono SQL Server. | Data Migration Assistant facilita l'individuazione di potenziali problemi che possono impedire la migrazione. Identifica le funzionalità non supportate, oltre alle nuove funzionalità da cui è possibile trarre vantaggio dopo la migrazione, e consente di individuare il percorso corretto per la migrazione del database. [Altre informazioni](/sql/dma/dma-overview)
**Servizio Migrazione del database di Azure** | Consente di eseguire la migrazione di database locali a macchine virtuali di Azure che eseguono SQL Server, il database SQL di Azure o Istanze gestite di SQL di Azure. | [Altre informazioni](../dms/dms-overview.md) sul Servizio Migrazione del database.
**Movere** | Valutazione dei server. | [Altre informazioni](#movere) su Movere.
**Web App Migration Assistant** | Consente di valutare app Web locali ed eseguirne la migrazione ad Azure. |  usare Migration Assistant per Servizio app di Azure per valutare i siti Web locali per la migrazione a Servizio app di Azure.<br/><br/> Usare Migration Assistant per eseguire la migrazione di app Web .NET e PHP ad Azure. Per altre informazioni, vedere [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).
**Azure Data Box** | Consente di eseguire la migrazione dei dati offline. | Usare i prodotti Azure Data Box per spostare grandi quantità di dati offline in Azure. [Altre informazioni](../databox/index.yml)

> [!NOTE]
> Se si usa Azure per enti pubblici, gli strumenti integrati esterni e le offerte degli ISV non possono inviare dati ai progetti di Azure Migrate. È possibile usare gli strumenti in modo indipendente.

## <a name="isv-integration"></a>Integrazione di strumenti ISV

Azure Migrate si integra con diverse offerte ISV. 

**ISV**    | **Funzionalità**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrazione dei server.
[Cloudamize](https://www.cloudamize.com/platform) | Valutazione dei server.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Valutazione e migrazione dei server.
[Device42](https://docs.device42.com/) | Valutazione dei server.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Valutazione di VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrazione dei server.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Valutazione dei server.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Valutazione di server e database.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Valutazione server

Lo strumento Azure Migrate: Lo strumento Valutazione server consente di individuare e valutare le macchine virtuali VMware, le macchine virtuali Hyper-V e i server fisici per la migrazione ad Azure. 

Ecco le operazioni che esegue:

- **Idoneità per Azure**: valuta se i computer locali sono pronti per la migrazione ad Azure.
- **Definizione delle dimensioni per Azure**: stima le dimensioni delle macchine virtuali di Azure o il numero di nodi VMware di Azure dopo la migrazione.
- **Stima dei costi di Azure**: ottenere una stima dei costi per l'esecuzione di server locali in Azure.
- **Analisi delle dipendenze**: identifica le dipendenze tra server e le strategie di ottimizzazione per il trasferimento di server interdipendenti ad Azure. Altre informazioni su Valutazione server con l'[analisi delle dipendenze](concepts-dependency-visualization.md).

Valutazione server si basa su un'[appliance leggera di Azure Migrate](migrate-appliance.md) distribuita in locale.

- L'appliance viene eseguita in una macchina virtuale o in un server fisico. È possibile installarla facilmente usando un modello scaricato.
- L'appliance individua i computer locali. Invia inoltre continuamente metadati e dati sulle prestazioni dei computer ad Azure Migrate.
- L'individuazione dell'appliance è senza agente. Non viene installato nulla nei computer individuati.
- Dopo l'individuazione eseguita dall'appliance, è possibile riunire i computer individuati in gruppi ed eseguire valutazioni per ogni gruppo.

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://go.microsoft.com/fwlink/?linkid=2155668) per creare un progetto nell'area **Australia orientale** . Se si dispone già di un progetto in Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Strumento Migrazione server

Lo strumento Azure Migrate: Migrazione server consente di eseguire la migrazione ad Azure di:

**Migrazione** | **Dettagli**
--- | ---
Macchine virtuali VMware locali | Eseguire la migrazione di VM ad Azure con la migrazione senza agente o basata su agente.<br/><br/> Per la migrazione senza agente, lo strumento Migrazione del server usa la stessa appliance di Azure Migrate che può essere usata anche da Valutazione server per l'individuazione e la valutazione delle VM VMware.<br/><br/> Per la migrazione basata su agente, Migrazione del server usa un'appliance di replica.
VM Hyper-V locali | Eseguire la migrazione di VM ad Azure.<br/><br/> Per la migrazione, Migrazione del server usa gli agenti del provider installati nell'host Hyper-V.
Server fisici in locale | È possibile eseguire la migrazione di computer fisici ad Azure. È anche possibile eseguire la migrazione di altri computer virtualizzati, oltre che di VM di altri cloud pubblici, considerandoli come server fisici ai fini della migrazione. | Per la migrazione, Migrazione del server usa un'appliance di replica.


## <a name="selecting-assessment-and-migration-tools"></a>Selezione degli strumenti di valutazione e migrazione

Nell'hub di Azure Migrate selezionare lo strumento da usare per la valutazione o per la migrazione e aggiungerlo a un progetto di Azure Migrate. Se si aggiunge uno strumento ISV o Movere:

- Per iniziare, ottenere una licenza o iscriversi per ricevere una versione di valutazione gratuita seguendo le istruzioni dello strumento. Ogni ISV o strumento specifica la licenza per lo strumento.
- In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Seguire le istruzioni per connettersi.
- È possibile tenere traccia della migrazione in tutti gli strumenti dall'interno del progetto di Azure Migrate.

## <a name="movere"></a>Movere

Movere è una piattaforma SaaS (Software as a Service) che incrementa la business intelligence presentando in modo accurato interi ambienti IT nell'arco di una sola giornata. Le organizzazioni e le aziende crescono, cambiano e si evolvono dal punto di vista digitale. In questo contesto Movere permette agli utenti di visualizzare e controllare gli ambienti in modo affidabile, indipendentemente dalla piattaforma, dall'applicazione o dall'area geografica.

Movere è stata [acquisita](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) da Microsoft e non viene più venduta come offerta autonoma. Movere è disponibile tramite i programmi Microsoft Solution Assessment e Microsoft Cloud Economics. [Altre informazioni](https://www.movere.io) su Movere.

Si consiglia inoltre di esaminare Azure Migrate, il servizio di migrazione predefinito di Microsoft. Azure Migrate offre un hub centrale per semplificare la migrazione al cloud. L'hub supporta in modo completo i carichi di lavoro come server fisici e virtuali, database e applicazioni. La visibilità end-to-end consente di tenere traccia facilmente dello stato di avanzamento durante le fasi di individuazione, valutazione e migrazione.

Grazie a strumenti ISV incorporati di Azure e dei partner, Azure Migrate offre una vasta gamma di funzionalità, tra cui:

- Individuazione di server fisici e virtuali.
- Ridimensionamento basato sulle prestazioni.
- Pianificazione dei costi.
- Valutazioni basate su importazioni.
- Analisi delle dipendenze delle applicazioni senza agente.

Se si vuole assistenza qualificata per iniziare, Microsoft propone diversi [provider di servizi gestiti esperti di Azure](https://azure.microsoft.com/partners) a cui affidarsi. Visitare il [sito Web di Azure Migrate](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate.

- **Versione corrente**: usare questa versione per creare progetti di Azure Migrate, individuare i computer locali e orchestrare valutazioni e migrazioni. [Vedere altre informazioni](whats-new.md) sulle novità di questa versione.
- **Versione precedente**: la versione precedente di Azure migrate, nota anche come Azure migrate classica, supporta solo la valutazione di macchine virtuali VMware locali. Il Azure Migrate classico verrà ritirato a febbraio 2024. Dopo il 2024 febbraio, la versione classica di Azure Migrate non sarà più supportata e i metadati di inventario nei progetti classici verranno eliminati. Non è possibile aggiornare i progetti o i componenti della versione precedente alla nuova versione. È necessario [creare un nuovo progetto di Azure Migrate](create-manage-projects.md) e [aggiungervi strumenti di valutazione e migrazione](./create-manage-projects.md). Usare le esercitazioni per comprendere come usare gli strumenti di valutazione e migrazione disponibili. Se si dispone di un'area di lavoro Log Analytics collegata a un progetto classico, è possibile collegarla a un progetto di versione corrente dopo aver eliminato il progetto classico.

    Per accedere ai progetti esistenti, nel portale di Azure cercare e selezionare **Azure Migrate**. Nel dashboard di **Azure Migrate** sono disponibili una notifica e un collegamento per accedere ai progetti di Azure Migrate precedenti.

## <a name="next-steps"></a>Passaggi successivi

- Provare le esercitazioni per valutare le [VM VMware](./tutorial-discover-vmware.md), le [VM Hyper-V](./tutorial-discover-hyper-v.md) o i [server fisici](./tutorial-discover-physical.md).
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.