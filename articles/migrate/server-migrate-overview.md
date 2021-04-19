---
title: Selezionare un'opzione di migrazione VMware con Azure Migrate Server
description: Offre una panoramica delle opzioni per la migrazione di macchine virtuali VMware ad Azure con Azure Migrate Server Migration
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714165"
---
# <a name="select-a-vmware-migration-option"></a>Selezionare un'opzione di migrazione VMware

È possibile eseguire la migrazione di macchine virtuali VMware in Azure usando lo strumento Migrazione server di Azure Migrate. Questo strumento offre due opzioni per la migrazione di VM VMware:

- Migrazione con replica senza agente. Consente di eseguire la migrazione delle macchine virtuali senza che siano necessarie installazioni nelle VM stesse.
- Migrazione con un agente per la replica. Installare un agente nella VM per la replica.


## <a name="compare-migration-methods"></a>Confrontare i metodi di migrazione

Usare questi confronti selezionati per decidere quale metodo usare. È anche possibile esaminare i requisiti di supporto completi per la [migrazione senza agente](migrate-support-matrix-vmware-migration.md#agentless-migration) e basata [su](migrate-support-matrix-vmware-migration.md#agent-based-migration) agente.

**Impostazione** | **Senza agente** | **Basato su agente**
--- | --- | ---
**Autorizzazioni di Azure** | Sono necessarie autorizzazioni per creare un progetto Azure Migrate e per registrare Azure AD app create quando si distribuisce l'appliance Azure Migrate dispositivo. | Sono necessarie le autorizzazioni di collaboratore per la sottoscrizione di Azure. 
**Replica** | È possibile replicare contemporaneamente un massimo di 500 macchine virtuali da un server vCenter. Nel portale è possibile selezionare un massimo di 10 computer contemporaneamente per la replica. Per replicare più computer, aggiungerli in batch di 10.| La capacità di replica aumenta ridimensionando l'appliance di replica.
**Distribuzione dell'appliance** | [L Azure Migrate appliance](migrate-appliance.md) viene distribuita in locale. | [L Azure Migrate'appliance di](migrate-replication-appliance.md) replica viene distribuita in locale.
**Site Recovery compatibilità** | Compatibile. | Non è possibile eseguire la replica con Azure Migrate server se è stata impostata la replica per un computer usando Site Recovery.
**Disco di destinazione** | Dischi gestiti | Dischi gestiti
**Limiti del disco** | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 32 TB<br/><br/> Numero massimo di dischi: 60 | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 32 TB<br/><br/> Numero massimo di dischi: 63
**Dischi pass-through** | Non supportato | Supportato
**Avvio UEFI** | Supportata. | Supportata. 
**Connettività** | Internet pubblico <br/> ExpressRoute con peering Microsoft <br/> <br/> [Informazioni su](./replicate-using-expressroute.md) come usare endpoint privati per la replica tramite un peering privato ExpressRoute o una connessione VPN da sito a sito. |Internet pubblico <br/> ExpressRoute con peering privato <br/> ExpressRoute con peering Microsoft <br/> VPN da sito a sito

## <a name="compare-deployment-steps"></a>Confrontare i passaggi di distribuzione

Dopo aver esaminato le limitazioni, la comprensione dei passaggi necessari per la distribuzione di ogni soluzione può essere utile per decidere quale opzione scegliere.

**Attività** | **Dettagli** |**Senza agente** | **Basato su agente**
--- | --- | --- | ---
**Distribuire l'appliance Azure Migrate** | Un'appliance leggera che viene eseguita in una macchina virtuale VMware.<br/><br/> L'appliance viene usata per individuare e valutare i computer e per eseguire la migrazione dei computer usando la migrazione senza agente. | Obbligatorio.<br/><br/> Se l'appliance è già stata impostata per la valutazione, è possibile usare la stessa appliance per la migrazione senza agente. | Non obbligatorio.<br/><br/> Se è stata impostata un'appliance per la valutazione, è possibile lasciarla o rimuoverla al termine della valutazione.
**Usare lo strumento Valutazione server** | Valutare i computer con lo strumento Azure Migrate:Server Assessment. | La valutazione è facoltativa. | La valutazione è facoltativa.
**Usare lo strumento migrazione server** | Aggiungere lo strumento Azure Migrate Migrazione server nel progetto Azure Migrate server. | Obbligatoria | Obbligatoria
**Preparare VMware per la migrazione** | Configurare le impostazioni nei server VMware e nelle macchine virtuali. | Obbligatoria | Obbligatoria
**Installare il servizio di mobilità nelle macchine virtuali** | servizio di mobilità in ogni macchina virtuale da replicare | Facoltativo | Obbligatoria
**Distribuire l'appliance di replica** | [L'appliance di](migrate-replication-appliance.md) replica viene usata per la migrazione basata su agente. Si connette tra l'servizio di mobilità in esecuzione nelle macchine virtuali e la migrazione del server. | Facoltativo | Obbligatoria
**Replicare le macchine virtuali**. Abilitare la replica delle macchine virtuali. | Configurare le impostazioni di replica e selezionare le macchine virtuali da replicare | Obbligatoria | Obbligatoria
**Eseguire una migrazione di test** | Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto. | Obbligatoria | Obbligatoria
**Eseguire una migrazione completa** | Eseguire la migrazione delle macchine virtuali. | Obbligatoria | Obbligatoria



## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione di macchine virtuali VMware](tutorial-migrate-vmware.md) con migrazione senza agente.



