---
title: Architettura dell'appliance di Azure Migrate
description: Viene fornita una panoramica dell'appliance Azure Migrate utilizzata per l'individuazione, la valutazione e la migrazione dei server.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: f3a94576ef58eabf9d747c6e6c3a6372569d4cf1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785241"
---
# <a name="azure-migrate-appliance-architecture"></a>Architettura dell'appliance di Azure Migrate

Questo articolo descrive l'architettura e i processi di Azure Migrate Appliance. Il Azure Migrate Appliance è un'appliance semplice distribuita in locale per individuare i server per la migrazione ad Azure.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

L'appliance di Azure Migrate viene usata negli scenari seguenti.

**Scenario** | **Strumento** | **Utilizzato per**
--- | --- | ---
**Individuazione e valutazione dei server in esecuzione nell'ambiente VMware** | Azure Migrate: individuazione e valutazione | Individuare i server in esecuzione nell'ambiente VMware<br/><br/> Eseguire l'individuazione dell'inventario software installato, l'analisi delle dipendenze senza agenti e individuare SQL Server istanze e database.<br/><br/> Raccolta dei metadati delle prestazioni e della configurazione del server per le valutazioni.
**Migrazione senza agenti di server in esecuzione nell'ambiente VMware** | Azure Migrate: Migrazione del server | Individuare i server in esecuzione nell'ambiente VMware.<br/><br/> Replicare i server senza installare agenti su di essi.
**Individuazione e valutazione dei server in esecuzione nell'ambiente Hyper-V** | Azure Migrate: individuazione e valutazione | Individuare i server in esecuzione nell'ambiente Hyper-V.<br/><br/> Raccolta dei metadati delle prestazioni e della configurazione del server per le valutazioni.
**Individuazione e valutazione dei server fisici o virtualizzati in locale** |  Azure Migrate: individuazione e valutazione |  Individuare i server fisici o virtualizzati locali.<br/><br/> Raccolta dei metadati delle prestazioni e della configurazione del server per le valutazioni.

## <a name="deployment-methods"></a>Metodi di distribuzione

L'appliance può essere distribuita usando due metodi:

- Il dispositivo può essere distribuito usando un modello per i server in esecuzione nell'ambiente VMware o Hyper-V ([modello OVA per VMware](how-to-set-up-appliance-vmware.md) o [VHD per Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Se non si vuole usare un modello, è possibile distribuire l'appliance per l'ambiente VMware o Hyper-V usando uno [script del programma di installazione di PowerShell](deploy-appliance-script.md).
- In Azure per enti pubblici è necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](deploy-appliance-script-government.md).
- Per i server fisici o virtualizzati in locale o in qualsiasi altro cloud, è sempre necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](how-to-set-up-appliance-physical.md).
- I collegamenti di download sono disponibili nelle tabelle seguenti.

## <a name="appliance-services"></a>Servizi Appliance

L'Appliance dispone dei servizi seguenti:

- **Gestione configurazione Appliance**: si tratta di un'applicazione Web che può essere configurata con i dettagli dell'origine per avviare l'individuazione e la valutazione dei server. 
- **Agente di individuazione**: l'agente raccoglie i metadati di configurazione del server che possono essere usati per creare come valutazioni locali.
- **Agente di valutazione**: l'agente raccoglie i metadati delle prestazioni del server che possono essere usati per creare valutazioni basate sulle prestazioni.
- **Servizio di aggiornamento automatico**: il servizio mantiene aggiornati tutti gli agenti in esecuzione nell'appliance. Viene eseguito automaticamente una volta ogni 24 ore.
- **Agente DRA**: Orchestra la replica del server e coordina la comunicazione tra i server replicati e Azure. Usato solo quando si esegue la replica di server in Azure usando la migrazione senza agente.
- **Gateway**: invia i dati replicati ad Azure. Usato solo quando si esegue la replica di server in Azure usando la migrazione senza agente.
- **SQL Discovery and Assessment Agent**: Invia i metadati di configurazione e delle prestazioni di SQL Server istanze e database in Azure.

> [!Note]
> Gli ultimi 3 servizi sono disponibili solo nell'appliance usato per l'individuazione e la valutazione dei server in esecuzione nell'ambiente VMware.<br/> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto nell'Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

## <a name="discovery-and-collection-process"></a>Processo di individuazione e raccolta

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Architettura del dispositivo":::

L'appliance comunica con le origini di individuazione usando il processo seguente.

**Processo** | **Appliance VMware** | **Appliance Hyper-V** | **Appliance fisica**
---|---|---|---
**Avvia individuazione** | Per impostazione predefinita, l'appliance comunica con il server vCenter sulla porta TCP 443. Se il server vCenter è in ascolto su una porta diversa, è possibile configurarla in Gestione configurazione Appliance. | L'appliance comunica con gli host Hyper-V sulla porta WinRM 5985 (HTTP). | L'appliance comunica con i server Windows attraverso la porta WinRM 5985 (HTTP) con server Linux sulla porta 22 (TCP).
**Raccogliere i metadati di configurazione e prestazioni** | L'appliance raccoglie i metadati dei server in esecuzione in server vCenter usando le API vSphere connettendosi sulla porta 443 (porta predefinita) o su qualsiasi altra porta server vCenter in ascolto. | L'appliance raccoglie i metadati dei server in esecuzione negli host Hyper-V usando una sessione di Common Information Model (CIM) con host sulla porta 5985.| Il dispositivo raccoglie i metadati dai server Windows usando la sessione di Common Information Model (CIM) con server sulla porta 5985 e da server Linux con connettività SSH sulla porta 22.
**Inviare i dati di individuazione** | Il dispositivo invia i dati raccolti a Azure Migrate: individuazione e valutazione e Azure Migrate: migrazione del server sulla porta SSL 443.<br/><br/> Il dispositivo può connettersi ad Azure tramite Internet o tramite ExpressRoute (richiede il peering Microsoft). | Il dispositivo invia i dati raccolti a Azure Migrate: individuazione e valutazione sulla porta SSL 443.<br/><br/> Il dispositivo può connettersi ad Azure tramite Internet o tramite ExpressRoute (richiede il peering Microsoft).| Il dispositivo invia i dati raccolti a Azure Migrate: individuazione e valutazione sulla porta SSL 443.<br/><br/> Il dispositivo può connettersi ad Azure tramite Internet o tramite ExpressRoute (richiede il peering Microsoft).
**Frequenza di raccolta dati** | I metadati di configurazione vengono raccolti e inviati ogni 30 minuti. <br/><br/> I metadati delle prestazioni vengono raccolti ogni 20 secondi e vengono aggregati per l'invio di un punto dati ad Azure ogni 10 minuti. <br/><br/> I dati di inventario software vengono inviati ad Azure una volta ogni 12 ore. <br/><br/> I dati delle dipendenze senza agenti vengono raccolti ogni 5 minuti, aggregati nel dispositivo e inviati ad Azure ogni 6 ore. <br/><br/> I dati di configurazione SQL Server vengono aggiornati ogni 24 ore e i dati sulle prestazioni vengono acquisiti ogni 30 secondi.| I metadati di configurazione vengono raccolti e inviati ogni 30 minuti. <br/><br/> I metadati delle prestazioni vengono raccolti ogni 30 secondi e vengono aggregati per l'invio di un punto dati ad Azure ogni 10 minuti.|  I metadati di configurazione vengono raccolti e inviati ogni 30 minuti. <br/><br/> I metadati delle prestazioni vengono raccolti ogni 5 minuti e vengono aggregati per l'invio di un punto dati ad Azure ogni 10 minuti.
**Valutazione e migrazione** | È possibile creare valutazioni dei metadati raccolti dal dispositivo usando Azure Migrate: strumento di individuazione e valutazione.<br/><br/>Inoltre, è possibile avviare la migrazione dei server in esecuzione nell'ambiente VMware utilizzando Azure Migrate: strumento di migrazione server per orchestrare la replica del server senza agente.| È possibile creare valutazioni dei metadati raccolti dal dispositivo usando Azure Migrate: strumento di individuazione e valutazione. | È possibile creare valutazioni dei metadati raccolti dal dispositivo usando Azure Migrate: strumento di individuazione e valutazione.

## <a name="next-steps"></a>Passaggi successivi

[Esaminare](migrate-appliance.md) la matrice di supporto dell'appliance.