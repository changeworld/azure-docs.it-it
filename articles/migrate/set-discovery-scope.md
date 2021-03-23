---
title: Impostare l'ambito per l'individuazione dei server in VMware con Azure Migrate
description: Viene descritto come impostare l'ambito di individuazione per i server ospitati in VMware assessment e migrazione con Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775358"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Impostazione dell'ambito di individuazione per i server nell'ambiente VMware

In questo articolo viene descritto come limitare l'ambito di individuazione per i server in ambiente VMware quando si:

- Individuazione dei server con l' [appliance Azure migrate](migrate-appliance-architecture.md) quando si usa lo strumento Azure migrate: individuazione e valutazione.
- Individuazione dei server con l' [appliance Azure migrate](migrate-appliance-architecture.md) quando si usa lo strumento di migrazione Azure migrate: Server, per la migrazione senza agenti di server da un ambiente VMware ad Azure.

Quando si configura il dispositivo, si connette a server vCenter e avvia l'individuazione. Prima di connettere l'appliance a server vCenter, è possibile limitare l'individuazione a data center server vCenter, cluster, una cartella di cluster, host, una cartella di host o singoli server. Per impostare l'ambito, assegnare le autorizzazioni per l'account usato dall'appliance per accedere al server vCenter.

## <a name="before-you-start"></a>Prima di iniziare

Se non è stato configurato un account utente vCenter che Azure Migrate USA per l'individuazione, effettuare questa operazione ora per la [valutazione](./tutorial-discover-vmware.md#prepare-vmware) o la [migrazione senza agenti](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Assegnare autorizzazioni e ruoli

È possibile assegnare autorizzazioni per gli oggetti di inventario VMware usando uno dei due metodi seguenti:

- Nell'account usato dall'appliance assegnare un ruolo con le autorizzazioni necessarie per gli oggetti che si desidera definire come ambito.
- In alternativa, assegnare un ruolo all'account a livello di data center e propagarsi agli oggetti figlio. Assegnare quindi all'account un ruolo **Nessun accesso** per ogni oggetto che non si vuole includere nell'ambito. Questo approccio non è consigliato perché è complesso e potrebbe esporre i controlli di accesso, perché a ogni nuovo oggetto figlio viene automaticamente concesso l'accesso ereditato dall'elemento padre.

Non è possibile definire l'ambito dell'individuazione dell'inventario a livello di cartella vCenter Server. Se è necessario definire l'ambito di individuazione per i server in una cartella, creare un utente e concedere l'accesso singolarmente a ogni server necessario. Sono supportate le cartelle host e cluster.


### <a name="assign-a-role-for-assessment"></a>Assegnazione di un ruolo per la valutazione

1. Nell'account vCenter dell'appliance usato per l'individuazione, applicare il ruolo di sola **lettura** per tutti gli oggetti padre che ospitano i server che si desidera individuare e valutare (host, cluster, cartella host, cartella cluster, fino a datacenter).
2. Propagare queste autorizzazioni agli oggetti figlio nella gerarchia.

    ![Assegnare le autorizzazioni](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Assegnazione di un ruolo per la migrazione senza agenti

1. Nell'account appliance vCenter usato per la migrazione, applicare un ruolo definito dall'utente che disponga delle [autorizzazioni necessarie](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), a tutti gli oggetti padre che ospitano i server di cui si vuole eseguire l'individuazione e la migrazione.
2. È possibile denominare il ruolo con un elemento più semplice da identificare. Ad esempio, <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>Soluzione per la restrizione della cartella del server

Attualmente, lo strumento Azure Migrate: individuazione e valutazione non riesce a individuare i server se l'accesso viene concesso a livello di cartella vCenter Server. Se si desidera definire l'ambito di individuazione e valutazione in base alle cartelle del server, utilizzare questa soluzione alternativa.

1. Assegnare autorizzazioni di sola lettura in tutti i server che si trovano nelle cartelle di cui si desidera definire l'ambito per l'individuazione e la valutazione.
2. Concedere l'accesso in sola lettura a tutti gli oggetti padre che ospitano i server host, cluster, cartella hosts, cartella clusters, fino al Data Center. Non è necessario propagare le autorizzazioni a tutti gli oggetti figlio.
3. Per usare le credenziali per l'individuazione, selezionare il Data Center come **ambito della raccolta**.


L'installazione del controllo degli accessi in base al ruolo garantisce che l'account utente vCenter corrispondente abbia accesso solo ai server specifici del tenant.


## <a name="next-steps"></a>Passaggi successivi

[Configurare l'appliance](how-to-set-up-appliance-vmware.md)