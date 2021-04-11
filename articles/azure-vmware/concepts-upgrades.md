---
title: Concetti-aggiornamenti e aggiornamenti del cloud privato
description: Informazioni sui processi e le funzionalità di aggiornamento principali della soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9810de40944f70a4efb7ec81d17868ffdf256c7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586149"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Aggiornamenti e aggiornamenti del cloud privato della soluzione VMware di Azure

Un vantaggio dei cloud privati della soluzione VMware di Azure è la piattaforma gestita. La manutenzione include aggiornamenti automatici a un bundle software VMware convalidato per garantire l'uso della versione più recente del software cloud privato della soluzione VMware di Azure.

In particolare, un cloud privato della soluzione VMware di Azure include:

- Nodi server bare metal dedicati sottoposti a provisioning con VMware ESXi hypervisor 
- server vCenter per la gestione di ESXi e rete VSAN 
- VMware NSX-T Software Defined Networking per macchine virtuali con carico di lavoro vSphere  
- Archivio dati VMware rete VSAN per macchine virtuali con carico di lavoro vSphere  
- HCX VMware per la mobilità del carico di lavoro  

Un cloud privato della soluzione VMware di Azure include anche risorse nel sottoposto di Azure necessario per la connettività e per il funzionamento del cloud privato. La soluzione VMware di Azure monitora costantemente l'integrità dei componenti sottoposto e VMware. Quando la soluzione VMware di Azure rileva un errore, viene eseguita un'azione per ripristinare i componenti non riusciti. 

## <a name="what-components-get-updated"></a>Quali componenti vengono aggiornati?   

La soluzione VMware di Azure aggiorna i componenti VMware seguenti: 

- server vCenter e ESXi in esecuzione nei nodi del server bare metal 
- vSAN 
- NSX-T 

La soluzione VMware di Azure aggiorna anche il software nel sottosistema, ad esempio i driver, il software sui commutatori di rete e il firmware nei nodi bare metal. 

## <a name="types-of-updates"></a>Tipi di aggiornamenti

La soluzione VMware di Azure applica i tipi di aggiornamenti seguenti ai componenti VMware:

- Patch: patch di sicurezza e correzioni di bug rilasciate da VMware. 
- Aggiornamenti: aggiornamenti della versione secondaria di uno o più componenti VMware. 
- Aggiornamenti: aggiornamenti della versione principale di uno o più componenti VMware.

Si riceverà una notifica prima e dopo l'applicazione delle patch ai cloud privati. Si collaborerà anche con l'utente per pianificare una finestra di manutenzione prima di applicare aggiornamenti o aggiornamenti al cloud privato. 

## <a name="vmware-appliance-backup"></a>Backup di appliance VMware 

La soluzione VMware di Azure esegue anche un backup della configurazione dei componenti VMware seguenti:

- Server vCenter 
- Gestione NSX-T 

In momenti di errore, la soluzione VMware di Azure può ripristinare questi componenti dal backup della configurazione. 

## <a name="vmware-software-versions"></a>Versioni del software VMware
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati illustrati i processi e le funzionalità di aggiornamento principali della soluzione VMware di Azure, è possibile che si vogliano ottenere informazioni su:

- [Come creare un cloud privato](tutorial-create-private-cloud.md).
- [Come abilitare la risorsa della soluzione VMware di Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
