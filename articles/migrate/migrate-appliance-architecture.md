---
title: Architettura dell'appliance di Azure Migrate
description: Offre una panoramica dell'appliance Azure Migrate usata per l'individuazione, la valutazione e la migrazione dei server.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715011"
---
# <a name="azure-migrate-appliance-architecture"></a>Architettura dell'appliance di Azure Migrate

Questo articolo descrive l'architettura e i Azure Migrate dell'appliance. L Azure Migrate appliance è un'appliance leggera distribuita in locale per individuare i server per la migrazione ad Azure.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

L'appliance di Azure Migrate viene usata negli scenari seguenti.

**Scenario** | **Strumento** | **Usato per**
--- | --- | ---
**Individuazione e valutazione dei server in esecuzione nell'ambiente VMware** | Azure Migrate: Individuazione e valutazione | Individuare i server in esecuzione nell'ambiente VMware<br/><br/> Eseguire l'individuazione dell'inventario software installato, l'analisi delle dipendenze senza agente e individuare SQL Server e database.<br/><br/> Raccogliere i metadati di configurazione e prestazioni del server per le valutazioni.
**Migrazione senza agente dei server in esecuzione nell'ambiente VMware** | Azure Migrate: Migrazione del server | Individuare i server in esecuzione nell'ambiente VMware.<br/><br/> Replicare i server senza installarvi agenti.
**Individuazione e valutazione dei server in esecuzione nell'ambiente Hyper-V** | Azure Migrate: Individuazione e valutazione | Individuare i server in esecuzione nell'ambiente Hyper-V.<br/><br/> Raccogliere i metadati di configurazione e prestazioni del server per le valutazioni.
**Individuazione e valutazione di server fisici o virtualizzati in locale** |  Azure Migrate: Individuazione e valutazione |  Individuare server fisici o virtualizzati in locale.<br/><br/> Raccogliere i metadati di configurazione e prestazioni del server per le valutazioni.

## <a name="deployment-methods"></a>Metodi di distribuzione

L'appliance può essere distribuita usando due metodi:

- L'appliance può essere distribuita usando un modello per i server in esecuzione nell'ambiente VMware o Hyper-V (modello[OVA per VMware](how-to-set-up-appliance-vmware.md) o [VHD per Hyper-V).](how-to-set-up-appliance-hyper-v.md)
- Se non si vuole usare un modello, è possibile distribuire l'appliance per l'ambiente VMware o Hyper-V usando uno script del programma [di installazione di PowerShell.](deploy-appliance-script.md)
- In Azure per enti pubblici, è necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](deploy-appliance-script-government.md).
- Per i server fisici o virtualizzati in locale o in qualsiasi altro cloud, si distribuisce sempre l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](how-to-set-up-appliance-physical.md).
- I collegamenti di download sono disponibili nelle tabelle seguenti.

## <a name="appliance-services"></a>Servizi appliance

L'appliance dispone dei servizi seguenti:

- **Gestione configurazione appliance:** si tratta di un'applicazione Web che può essere configurata con i dettagli di origine per avviare l'individuazione e la valutazione dei server. 
- **Agente di individuazione:** l'agente raccoglie i metadati di configurazione del server che possono essere usati per creare valutazioni locali.
- **Agente di valutazione:** l'agente raccoglie i metadati delle prestazioni del server che possono essere usati per creare valutazioni basate sulle prestazioni.
- **Servizio di aggiornamento automatico:** il servizio mantiene aggiornati tutti gli agenti in esecuzione nell'appliance. Viene eseguito automaticamente una volta ogni 24 ore.
- **Agente DRA:** orchestra la replica del server e coordina la comunicazione tra i server replicati e Azure. Usato solo quando si esegue la replica di server in Azure usando la migrazione senza agente.
- **Gateway**: invia i dati replicati ad Azure. Usato solo quando si esegue la replica di server in Azure usando la migrazione senza agente.
- **Agente di individuazione e valutazione SQL:** invia i metadati di configurazione e prestazioni SQL Server istanze e database ad Azure.

> [!Note]
> Gli ultimi 3 servizi sono disponibili solo nell'appliance usata per l'individuazione e la valutazione dei server in esecuzione nell'ambiente VMware.<br/> L'individuazione e la valutazione SQL Server istanze e database in esecuzione nell'ambiente VMware è ora disponibile in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto nell'Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

## <a name="discovery-and-collection-process"></a>Processo di individuazione e raccolta

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Architettura dell'appliance":::

L'appliance comunica con le origini di individuazione usando il processo seguente.

**Processo** | **Appliance VMware** | **Appliance Hyper-V** | **Appliance fisica**
---|---|---|---
**Avviare l'individuazione** | L'appliance comunica con il server vCenter sulla porta TCP 443 per impostazione predefinita. Se il server vCenter è in ascolto su una porta diversa, è possibile configurarlo in Gestione configurazione appliance. | L'appliance comunica con gli host Hyper-V sulla porta WinRM 5985 (HTTP). | L'appliance comunica con i server Windows sulla porta WinRM 5985 (HTTP) con i server Linux sulla porta 22 (TCP).
**Raccogliere i metadati di configurazione e prestazioni** | L'appliance raccoglie i metadati dei server in esecuzione in server vCenter usando le API vSphere connettendosi alla porta 443 (porta predefinita) o a qualsiasi altra porta su cui server vCenter è in ascolto. | L'appliance raccoglie i metadati dei server in esecuzione negli host Hyper-V usando una sessione Common Information Model (CIM) con host sulla porta 5985.| L'appliance raccoglie i metadati dai server Windows usando una sessione Common Information Model (CIM) con server sulla porta 5985 e da server Linux usando la connettività SSH sulla porta 22.
**Inviare i dati di individuazione** | L'appliance invia i dati raccolti a Azure Migrate: Individuazione e valutazione e Azure Migrate: Migrazione del server sulla porta SSL 443.<br/><br/>  L'appliance può connettersi ad Azure tramite Internet o tramite peering privato ExpressRoute o circuiti di peering Microsoft. | L'appliance invia i dati raccolti a Azure Migrate: individuazione e valutazione sulla porta SSL 443.<br/><br/> L'appliance può connettersi ad Azure tramite Internet o tramite peering privato ExpressRoute o circuiti di peering Microsoft. | L'appliance invia i dati raccolti a Azure Migrate: individuazione e valutazione sulla porta SSL 443.<br/><br/> L'appliance può connettersi ad Azure tramite Internet o tramite peering privato ExpressRoute o circuiti di peering Microsoft. 
**Frequenza raccolta dati** | I metadati di configurazione vengono raccolti e inviati ogni 30 minuti. <br/><br/> I metadati delle prestazioni vengono raccolti ogni 20 secondi e aggregati per inviare un punto dati ad Azure ogni 10 minuti. <br/><br/> I dati di inventario software vengono inviati ad Azure una volta ogni 12 ore. <br/><br/> I dati sulle dipendenze senza agente vengono raccolti ogni 5 minuti, aggregati nell'appliance e inviati ad Azure ogni 6 ore. <br/><br/> I SQL Server di configurazione vengono aggiornati ogni 24 ore e i dati sulle prestazioni vengono acquisiti ogni 30 secondi.| I metadati di configurazione vengono raccolti e inviati ogni 30 minuti. <br/><br/> I metadati delle prestazioni vengono raccolti ogni 30 secondi e aggregati per inviare un punto dati ad Azure ogni 10 minuti.|  I metadati di configurazione vengono raccolti e inviati ogni 30 minuti. <br/><br/> I metadati delle prestazioni vengono raccolti ogni 5 minuti e aggregati per inviare un punto dati ad Azure ogni 10 minuti.
**Valutazione e migrazione** | È possibile creare valutazioni dai metadati raccolti dall'appliance usando Azure Migrate: Strumento di individuazione e valutazione.<br/><br/>È anche possibile avviare la migrazione di server in esecuzione nell'ambiente VMware usando Azure Migrate: Strumento di migrazione server per orchestrare la replica di server senza agente.| È possibile creare valutazioni dai metadati raccolti dall'appliance usando Azure Migrate: Strumento di individuazione e valutazione. | È possibile creare valutazioni dai metadati raccolti dall'appliance usando Azure Migrate: Strumento di individuazione e valutazione.

## <a name="next-steps"></a>Passaggi successivi

[Esaminare la](migrate-appliance.md) matrice di supporto dell'appliance.