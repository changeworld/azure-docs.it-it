---
title: Eliminare un progetto di Azure Migrate
description: Questo articolo illustra come eliminare un progetto Azure Migrate usando il portale di Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714741"
---
# <a name="delete-an-azure-migrate-project"></a>Eliminare un progetto di Azure Migrate

Questo articolo descrive come eliminare un progetto [Azure Migrate](./migrate-services-overview.md) progetto.


## <a name="before-you-start"></a>Prima di iniziare

Prima di eliminare un progetto:

- Quando si elimina un progetto, il progetto e i metadati del computer individuati vengono eliminati.
- Se è stata collegata un'area di lavoro Log Analytics a Server Assessment tool per l'analisi delle dipendenze, decidere se eliminare l'area di lavoro. 
    - L'area di lavoro non viene eliminata automaticamente. Eliminarla manualmente.
    - Verificare per quale area di lavoro viene usata prima di eliminarla. La stessa area di lavoro Log Analytics può essere usata per più scenari.
    - Prima di eliminare il progetto, è possibile trovare un collegamento all'area di lavoro in **Azure Migrate - Server** Azure Migrate - Valutazione server , in Area di lavoro  >   **OMS**.
    - Per eliminare un'area di lavoro dopo l'eliminazione di un progetto, individuare l'area di lavoro nel gruppo di risorse pertinente e seguire [queste istruzioni.](../azure-monitor/logs/delete-workspace.md)


## <a name="delete-a-project"></a>Eliminare un progetto


1. Nel portale di Azure aprire il gruppo di risorse in cui è stato creato il progetto.
2. Nella pagina del gruppo di risorse selezionare **Mostra tipi nascosti**.
3. Selezionare il progetto e le risorse associate da eliminare.
    - Il tipo di risorsa per Azure Migrate progetti è **Microsoft.Migrate/migrateprojects**.
    - Nella sezione successiva esaminare le risorse create per l'individuazione, la valutazione e la migrazione in un Azure Migrate progetto.
    - Se il gruppo di risorse contiene solo il Azure Migrate progetto, è possibile eliminare l'intero gruppo di risorse.
    - Se si vuole eliminare un progetto dalla versione precedente di Azure Migrate, i passaggi sono gli stessi. Il tipo di risorsa per questi progetti è **Progetto di migrazione**.


## <a name="created-resources"></a>Risorse create

Queste tabelle riepilogano le risorse create per l'individuazione, la valutazione e la migrazione in un Azure Migrate progetto.

> [!NOTE]
> Eliminare l'insieme di credenziali delle chiavi con cautela perché potrebbe contenere chiavi di sicurezza.

### <a name="projects-with-public-endpoint-connectivity"></a>Progetti con connettività di endpoint pubblici

#### <a name="vmwarephysical-server"></a>VMware/server fisico

**Risorsa** | **Tipo**
--- | ---
"Appliancename"kv | Insieme di credenziali delle chiavi
"Appliancename" site | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Insieme di credenziali dei servizi di ripristino
"ProjectName"-MigrateVault-* | Insieme di credenziali dei servizi di ripristino
migrateappligwsa* | Account di archiviazione
migrateapplilsa* | Account di archiviazione
migrateapplicsa* | Account di archiviazione
migrateapplikv* | Insieme di credenziali delle chiavi
migrateapplisbns* | Spazio dei nomi del bus di servizio

#### <a name="hyper-v-vm"></a>Macchina virtuale Hyper-V

**Risorsa** | **Tipo**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
HyperV*kv | Insieme di credenziali delle chiavi
Sito HyperV* | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Insieme di credenziali dei servizi di ripristino

<br/>
Le tabelle seguenti riepilogano le risorse create da Azure Migrate per individuare, valutare ed eseguire la migrazione di server in una rete privata usando il [collegamento privato di Azure.](./how-to-use-azure-migrate-with-private-endpoints.md)

### <a name="projects-with-private-endpoint-connectivity"></a>Progetti con connettività dell'endpoint privato

#### <a name="vmware-vms---agentless-migrations"></a>Macchine virtuali VMware : migrazioni senza agente

**Tipo** | **Risorsa** | **Endpoint privato <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Sito di individuazione (sito master) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName" \* project \* pe 
Insieme di credenziali delle chiavi | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | N/D
Insieme di credenziali dei servizi di ripristino | "ApplianceName"*vault | N/D
Account di archiviazione | "ApplianceName"*usa | "ApplianceName" \* usa \* pe
Insieme di credenziali dei servizi di ripristino | "ProjectName"-MigrateVault-* | N/D
Account di archiviazione | migrateappligwsa* | N/D
Account di archiviazione | migrateapplilsa* | N/D
Insieme di credenziali delle chiavi | migrateapplikv* | N/D
Spazio dei nomi del bus di servizio | migrateapplisbns* | N/D

#### <a name="hyper-v-vms"></a>VM Hyper-V 

**Tipo** | **Risorsa** | **Endpoint privato <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Sito di individuazione (sito master) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName" \* project \* pe 
Insieme di credenziali delle chiavi | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | N/D
Insieme di credenziali dei servizi di ripristino | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Server fisici/macchine virtuali AWS/macchine virtuali GCP 

**Tipo** | **Risorsa** | **Endpoint privato <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Sito di individuazione (sito master) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | "ApplianceName" \* project \* pe 
Insieme di credenziali delle chiavi | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | N/D
Insieme di credenziali dei servizi di ripristino | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere altri strumenti [di valutazione](how-to-assess.md) [e](how-to-migrate.md) migrazione. 
