---
title: Matrice di supporto di Azure Migrate
description: Informazioni riepilogative su impostazioni e limiti del supporto per il servizio Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: af0b8a4d3dfbce32e412f5294fb19ade61fd7661
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628170"
---
# <a name="azure-migrate-support-matrix"></a>Matrice di supporto di Azure Migrate

È possibile usare il [servizio Azure migrate](./migrate-services-overview.md) per valutare ed eseguire la migrazione dei server al cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e i limiti generali del supporto per gli scenari e le distribuzioni di Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Scenari di valutazione/migrazione supportati

Nella tabella sono riepilogati gli scenari di individuazione, valutazione e migrazione supportati.

**Distribuzione** | **Dettagli** 
--- | --- 
**Individuazione** | È possibile individuare i metadati del server e i dati dinamici delle prestazioni.
**App-individuazione** | È possibile individuare app, ruoli e funzionalità in esecuzione in macchine virtuali VMware. Questa funzionalità è attualmente limitata solo alla fase di individuazione, La valutazione è attualmente a livello di server. Non sono ancora disponibili valutazioni basate su app, ruoli o funzionalità. 
**Valutazione** | È possibile valutare carichi di lavoro e dati locali in esecuzione su macchine virtuali VMware, macchine virtuali Hyper-V e server fisici. Valutare l'utilizzo di Azure Migrate: server Assessment, Microsoft Data Migration Assistant (DMA), nonché altri strumenti e offerte ISV.
**Migrazione** | È possibile eseguire la migrazione in Azure di carichi di lavoro e dati in esecuzione su server fisici, macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e macchine virtuali basate su cloud. Eseguire la migrazione usando Azure Migrate: valutazione del server e il servizio migrazione del database di Azure (DMS), nonché altri strumenti e offerte ISV.

> [!NOTE]
> Attualmente, gli strumenti ISV non possono inviare dati ad Azure Migrate in Azure per enti pubblici. È possibile usufruire degli strumenti Microsoft integrati oppure usare strumenti partner in modo indipendente.

## <a name="supported-tools"></a>Strumenti supportati


Nella tabella seguente è riepilogato il supporto di strumenti specifici.

**Strumento** | **Valutazione** | **Migrazione** 
--- | --- | ---
Azure Migrate: valutazione del server | Valutare [macchine virtuali VMware](./tutorial-discover-vmware.md), [macchine virtuali Hyper-V](./tutorial-discover-hyper-v.md) e [server fisici](./tutorial-discover-physical.md). |  Non disponibile (N/d)
Azure Migrate: Migrazione server | N/D | Eseguire la migrazione di [macchine virtuali VMware](tutorial-migrate-vmware.md), [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) e [server fisici](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N/D | Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e altri carichi di lavoro cloud. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici e gli altri carichi di lavoro cloud. | N/D
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Valutare le macchine virtuali VMware, le VM Hyper-V, gli altri carichi di lavoro cloud di server fisici. |  Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e carichi di lavoro su cloud pubblico.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici e gli altri carichi di lavoro cloud.| N/D
[DMA](/sql/dma/dma-overview) | Valutare SQL Server database. | N/D
[Servizio Migrazione del database](../dms/dms-overview.md) | N/D | Eseguire la migrazione di SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Valutare Virtual Desktop Infrastructure (VDI) | N/D
[Movere](https://www.movere.io/) | Valutare le macchine virtuali VMware, le VM Hyper-V, le VM Xen, i server fisici, le workstation (incluso VDI) e altri carichi di lavoro cloud. | N/D
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N/D | Eseguire la migrazione di macchine virtuali VMWare, macchine virtuali Hyper-V, VM Xen, VM KVM, server fisici e altri carichi di lavoro cloud 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici e gli altri carichi di lavoro cloud. | N/D
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Valutare le macchine virtuali VMware, le VM Hyper-V, i server fisici e gli altri carichi di lavoro cloud e i database SQL Server. | N/D
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | Valutare app Web | Eseguire la migrazione di app Web.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | N/D |  Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e altri carichi di lavoro cloud.


## <a name="project"></a>Project

**Supporto** | **Dettagli**
--- | ---
Subscription | Può avere più progetti all'interno di una sottoscrizione.
Autorizzazioni di Azure | Per creare un progetto, l'utente deve disporre delle autorizzazioni di collaboratore o proprietario nella sottoscrizione.
VM VMware  | È possibile valutare fino a 35.000 macchine virtuali VMware in un singolo progetto.
VM Hyper-V    | È possibile valutare fino a 35.000 macchine virtuali Hyper-V in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.

## <a name="azure-permissions"></a>Autorizzazioni di Azure

Per Azure Migrate usare Azure, è necessario disporre di queste autorizzazioni prima di iniziare la valutazione e la migrazione dei server.

**Attività** | **Autorizzazioni** | **Dettagli**
--- | --- | ---
Creare un progetto | L'account di Azure necessita di autorizzazioni per creare un progetto. | Configurare per [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) o [server fisici](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Registrare l'appliance Azure Migrate| Azure Migrate usa un' [appliance di Azure migrate](migrate-appliance.md) Lightweight per valutare i server con Azure migrate: server assessment e per eseguire la [migrazione senza agenti](server-migrate-overview.md) di macchine virtuali VMware con Azure migrate: migrazione del server. Questa appliance individua i server e invia i metadati e i dati sulle prestazioni a Azure Migrate.<br/><br/> Durante la registrazione, i provider di servizi di registrazione (Microsoft.OffAzure, Microsoft.Migrate, and Microsoft.KeyVault) vengono registrati con la sottoscrizione scelta nell'appliance, in modo che interagisca correttamente con il provider di servizi di risorse. Per eseguire la registrazione, è necessario disporre dei diritti di accesso alla sottoscrizione come collaboratore o proprietario.<br/><br/> **VMware** - Nell'ambito dell'onboarding, Azure Migrate crea due app Azure Active Directory (Azure AD): la prima app consente la comunicazione tra gli agenti dell'appliance e il servizio Azure Migrate. L'app non ha le autorizzazioni necessarie per effettuare chiamate di gestione risorse di Azure o avere l'accesso RBAC di Azure per le risorse. La seconda app accede a un'istanza di Azure Key Vault creata nella sottoscrizione utente solo per la migrazione VMware senza agente. Nella migrazione senza agente, Azure Migrate crea un insieme di credenziali delle chiavi per gestire le chiavi di accesso all'account di archiviazione di replica nella sottoscrizione. L'accesso con controllo degli accessi in base al ruolo di Azure viene eseguito nel Azure Key Vault (nel tenant del cliente) quando viene avviata l'individuazione dal dispositivo.<br/><br/> **Hyper-V**  - Durante l'onboarding. Azure Migrate crea un'app Azure AD, che consente la comunicazione tra gli agenti dell'appliance e il servizio Azure Migrate. L'app non ha le autorizzazioni necessarie per effettuare chiamate di gestione risorse di Azure o avere l'accesso RBAC di Azure per le risorse. | Configurare per [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) o [server fisici](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Creare un insieme di credenziali delle chiavi per la migrazione senza agente VMware | Per eseguire la migrazione di macchine virtuali VMware con Azure Migrate senza agente: migrazione del server, Azure Migrate crea un Key Vault per gestire le chiavi di accesso all'account di archiviazione della replica nella sottoscrizione. Per creare l'insieme di credenziali, impostare le autorizzazioni (proprietario, collaboratore e amministratore accesso utenti) nel gruppo di risorse in cui risiede il progetto. | [Impostare](./tutorial-discover-vmware.md#prepare-an-azure-user-account) le autorizzazioni.

## <a name="supported-geographies-public-cloud"></a>Aree geografiche supportate (cloud pubblico)

È possibile creare un progetto in diverse aree geografiche nel cloud pubblico.

- Sebbene sia possibile creare progetti solo in queste aree geografiche, è possibile valutare o migrare i server per altri percorsi di destinazione.
- L'area geografica del progetto viene usata solo per archiviare i metadati individuati.
- Quando si crea un progetto, si seleziona un'area geografica. Il progetto e le risorse correlate vengono creati in una delle aree geografiche. L'area viene allocata dal servizio Azure Migrate.

**Area geografica** | **Posizione di archiviazione dei metadati**
--- | ---
Asia Pacifico | Asia sud-orientale o Asia orientale
Australia | Australia orientale o Australia sud-orientale
Brasile | Brasile meridionale
Canada | Canada centrale o Canada orientale
Europa | Europa settentrionale o Europa occidentale
Francia | Francia centrale
India | India centrale o India meridionale
Giappone |  Giappone orientale o Giappone occidentale
Corea del Sud | Corea centrale o Corea meridionale
Svizzera | Svizzera settentrionale
Regno Unito | Regno Unito meridionale o Regno Unito occidentale
Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2

> [!NOTE]
> Per la Svizzera geografia, Svizzera occidentale è disponibile solo per gli utenti dell'API REST ed è necessaria una sottoscrizione approvata.

## <a name="supported-geographies-azure-government"></a>Aree geografiche supportate (Azure per enti pubblici)

**Attività** | **Area geografica** | **Dettagli**
--- | --- | ---
Crea progetto | Stati Uniti | I metadati vengono archiviati in US Gov Arizona, US Gov Virginia
Valutazione di destinazione | Stati Uniti | Aree di destinazione: US Gov Arizona, US Gov Virginia, US Gov Texas
Replica di destinazione | Stati Uniti | Aree di destinazione: US DoD (area centrale), US DoD (area orientale), US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>Valutazione e migrazione VMware

[Esaminare](migrate-support-matrix-vmware.md) la Azure migrate: server Assessment e Azure migrate: matrice di supporto per la migrazione del server per macchine virtuali VMware.

## <a name="hyper-v-assessment-and-migration"></a>Valutazione e migrazione di Hyper-V

[Esaminare](migrate-support-matrix-hyper-v.md) la Azure migrate: server Assessment e Azure migrate: matrice di supporto per la migrazione del server per macchine virtuali Hyper-V.



## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: con questa versione è possibile creare nuovi progetti, individuare le valutazioni locali e orchestrare valutazioni e migrazioni. [Altre informazioni](whats-new.md)
- **Versione precedente**: ai clienti che usano la versione precedente di Azure Migrate (è supportata solo la valutazione di macchine virtuali VMware locali), si consiglia di usare ora la versione corrente. Nella versione precedente non è possibile creare nuovi progetti o eseguire nuove individuazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Valutare le macchine virtuali VMware](./tutorial-assess-vmware-azure-vm.md) per la migrazione.
- [Valutare le macchine virtuali Hyper-V](tutorial-assess-hyper-v.md) per la migrazione.
