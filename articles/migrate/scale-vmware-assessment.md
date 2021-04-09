---
title: Valutare un numero elevato di server nell'ambiente VMware per la migrazione ad Azure con Azure Migrate
description: Viene descritto come valutare un numero elevato di server nell'ambiente VMware per la migrazione ad Azure usando il servizio Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778254"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Valutare un numero elevato di server nell'ambiente VMware per la migrazione ad Azure


Questo articolo descrive come valutare numeri elevati (1000-35.000) di server locali nell'ambiente VMware per la migrazione ad Azure, usando lo strumento Azure Migrate Discovery and Assessment.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 

In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare VMware per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si vuole provare un modello di prova per valutare un paio di server prima di valutare la scalabilità, seguire la [serie di esercitazioni](./tutorial-discover-vmware.md)

## <a name="plan-for-assessment"></a>Pianificare la valutazione

Quando si pianifica la valutazione di un numero elevato di server nell'ambiente VMware, è necessario considerare alcuni aspetti:

- **Pianificare Azure migrate progetti**: scoprire come distribuire progetti di Azure migrate. Se, ad esempio, i Data Center si trovano in aree geografiche diverse oppure è necessario archiviare i metadati relativi a individuazione, valutazione o migrazione in un'altra area geografica, potrebbero essere necessari più progetti. 
- **Appliance del piano**: Azure migrate usa un'appliance Azure migrate locale, distribuita come macchina virtuale VMware, per individuare continuamente i server. Il dispositivo monitora le modifiche apportate all'ambiente, ad esempio l'aggiunta di server, dischi o schede di rete. Invia inoltre i metadati e i dati sulle prestazioni relativi ad Azure. È necessario scoprire quante Appliance è necessario distribuire.
- **Pianificare gli account per l'individuazione**: il Azure migrate Appliance usa un account con accesso a server vCenter per individuare i server per la valutazione e la migrazione. Se si stanno individuando più di 10.000 server, configurare più account come richiesto. non esiste alcuna sovrapposizione tra i server individuati da due appliance di un progetto. 

> [!NOTE]
> Se si configurano più appliance, assicurarsi che non vi sia sovrapposizione tra i server negli account vCenter forniti. Un'individuazione con una sovrapposizione di questo tipo è uno scenario non supportato. Se un server viene individuato da più di un appliance, si verificano duplicati nell'individuazione e nei problemi durante l'abilitazione della replica per il server usando il portale di Azure nella migrazione del server.

## <a name="planning-limits"></a>Limiti di pianificazione
 
Usare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti Azure Migrate** | Valutazione di un massimo di 35.000 server in un progetto.
**Appliance Azure Migrate** | Un dispositivo può individuare fino a 10.000 server in una server vCenter.<br/> Un appliance può connettersi solo a una singola server vCenter.<br/> Un appliance può essere associato solo a un singolo progetto Azure Migrate.<br/>  Un numero qualsiasi di Appliance può essere associato a un singolo progetto di Azure Migrate. <br/><br/> 
**Gruppo** | È possibile aggiungere fino a 35.000 server in un singolo gruppo.
**Valutazione Azure Migrate** | È possibile valutare fino a 35.000 server in una singola valutazione.

Tenendo conto di questi limiti, di seguito sono riportate alcune distribuzioni di esempio:


**Server vCenter** | **Server nel server** | **Consiglio** | **Azione**
---|---|---|---
Uno | < 10.000 | Un progetto Azure Migrate.<br/> Un appliance.<br/> Un account vCenter per l'individuazione. | Configurare il dispositivo, connettersi a server vCenter con un account.
Uno | > 10.000 | Un progetto Azure Migrate.<br/> Più appliance.<br/> Più account vCenter. | Configurare l'appliance per ogni server 10.000.<br/><br/> Configurare gli account vCenter e dividere l'inventario per limitare l'accesso per un account a meno di 10.000 server.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra i server individuati con dispositivi diversi. <br/> <br/> Assicurarsi che non vi sia sovrapposizione tra i server negli account vCenter forniti. Un'individuazione con una sovrapposizione di questo tipo è uno scenario non supportato. Se un server viene individuato da più di un appliance, si verificano duplicati nell'individuazione e nei problemi durante l'abilitazione della replica per il server usando il portale di Azure nella migrazione del server.
Più elementi | < 10.000 |  Un progetto Azure Migrate.<br/> Più appliance.<br/> Un account vCenter per l'individuazione. | Configurare le appliance, connettersi a server vCenter con un account.<br/> È possibile analizzare le dipendenze tra i server individuati con dispositivi diversi.
Più elementi | > 10.000 | Un progetto Azure Migrate.<br/> Più appliance.<br/> Più account vCenter. | Se server vCenter individuazione < Server 10.000, configurare un'appliance per ogni server vCenter.<br/><br/> Se server vCenter individuazione > Server 10.000, configurare un'appliance per ogni server 10.000.<br/> Configurare gli account vCenter e dividere l'inventario per limitare l'accesso per un account a meno di 10.000 server.<br/> Connettere ogni appliance al server vCenter con un account.<br/> È possibile analizzare le dipendenze tra i server individuati con dispositivi diversi. <br/><br/> Assicurarsi che non vi sia sovrapposizione tra i server negli account vCenter forniti. Un'individuazione con una sovrapposizione di questo tipo è uno scenario non supportato. Se un server viene individuato da più di un appliance, si verificano duplicati nell'individuazione e nei problemi durante l'abilitazione della replica per il server usando il portale di Azure nella migrazione del server.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Pianificare l'individuazione in un ambiente multi-tenant

Se si sta pianificando un ambiente multi-tenant, è possibile definire l'ambito dell'individuazione nel server vCenter.

- È possibile impostare l'ambito di individuazione dell'appliance su un server vCenter Data Center, cluster o cartella di cluster, host o cartella di host o singoli server.
- Se l'ambiente è condiviso tra i tenant e si vuole individuare separatamente ogni tenant, è possibile definire l'ambito di accesso all'account vCenter usato dall'appliance per l'individuazione. 
    - Se i tenant condividono gli host, può essere utile definire l'ambito per cartelle VM. Azure Migrate non è in grado di individuare i server se l'account vCenter ha accesso concesso a livello di cartella della macchina virtuale vCenter. Se si sta cercando di definire l'ambito di individuazione per cartelle VM, è possibile farlo assicurando che all'account vCenter sia assegnato l'accesso in sola lettura a livello di server. [Altre informazioni](set-discovery-scope.md)

## <a name="prepare-for-assessment"></a>Preparare la valutazione

Preparare Azure e VMware per l'individuazione e lo strumento di valutazione:

1. Verificare i [requisiti e le limitazioni del supporto VMware](migrate-support-matrix-vmware.md).
2. Configurare le autorizzazioni per l'account di Azure per interagire con Azure Migrate.
3. Preparare VMware per la valutazione.

Per configurare queste impostazioni, seguire le istruzioni riportate in [questa esercitazione](./tutorial-discover-vmware.md) .


## <a name="create-a-project"></a>Creare un progetto

In conformità ai requisiti di pianificazione, eseguire le operazioni seguenti:

1. Creare un progetto Azure Migrate.
2. Aggiungere lo strumento di individuazione e valutazione Azure Migrate ai progetti.

[Scopri di più](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Creare ed esaminare una valutazione

1. Creare valutazioni per i server nell'ambiente VMware.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.


Per configurare queste impostazioni, seguire le istruzioni riportate in [questa esercitazione](./tutorial-assess-vmware-azure-vm.md) .
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificazione della scalabilità di Azure Migrate valutazioni per i server in ambiente VMware
> * Azure e VMware preparati per la valutazione
> * Creazione di un progetto Azure Migrate ed esecuzione di valutazioni
> * Revisione delle valutazioni in preparazione alla migrazione.

A questo punto, [informazioni su come](concepts-assessment-calculation.md) vengono calcolate le valutazioni e su come [modificare le valutazioni](how-to-modify-assessment.md).