---
title: Concetti - Aggiornamenti e aggiornamenti del cloud privato
description: Informazioni sui processi e le funzionalità di aggiornamento chiave in soluzione Azure VMware.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ced5832a6d994f6cbc7e659d44ce4f6ac88681d0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876815"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>soluzione Azure VMware aggiornamenti del cloud privato

Uno dei vantaggi soluzione Azure VMware cloud privati è che la piattaforma viene gestita automaticamente. La manutenzione include aggiornamenti automatici a un bundle software convalidato VMware per garantire l'uso della versione più recente soluzione Azure VMware software cloud privato.

In particolare, un soluzione Azure VMware cloud privato include:

- Nodi server bare metal dedicati di cui è stato effettuato il provisioning VMware ESXi hypervisor 
- Server vCenter per la gestione di ESXi e vSAN 
- VMware NSX-T software defined networking for vSphere workload VMs  
- Archivio dati VMware vSAN per macchine virtuali con carico di lavoro vSphere  
- VMware HCX per la mobilità dei carichi di lavoro  

Un soluzione Azure VMware cloud privato include anche le risorse del livello sottostante di Azure necessarie per la connettività e per il funzionamento del cloud privato. soluzione Azure VMware monitora continuamente l'integrità dei componenti sottostanti e VMware. Quando soluzione Azure VMware rileva un errore, esegue un'azione per ripristinare i componenti non riusciti. 

## <a name="what-components-get-updated"></a>Quali componenti vengono aggiornati?   

soluzione Azure VMware aggiorna i componenti VMware seguenti: 

- server vCenter ed ESXi in esecuzione nei nodi del server bare metal 
- vSAN 
- NSX-T 

soluzione Azure VMware aggiorna anche il software nel livello sottostante, ad esempio i driver, il software nei commutatori di rete e il firmware nei nodi bare metal. 

## <a name="types-of-updates"></a>Tipi di aggiornamenti

soluzione Azure VMware applica i tipi di aggiornamenti seguenti ai componenti VMware:

- Patch: patch di sicurezza e correzioni di bug rilasciate da VMware. 
- Aggiornamenti: aggiornamenti delle versioni secondarie di uno o più componenti VMware. 
- Aggiornamenti: aggiornamenti delle versioni principali di uno o più componenti VMware.

Si verrà informati prima e dopo l'applicazione delle patch ai cloud privati. Si collaborare anche con l'utente per pianificare una finestra di manutenzione prima di applicare aggiornamenti o aggiornamenti al cloud privato. 

## <a name="vmware-appliance-backup"></a>Backup dell'appliance VMware 

soluzione Azure VMware anche un backup della configurazione dei componenti VMware seguenti:

- Server vCenter 
- NSX-T Manager 

In caso di errore, soluzione Azure VMware ripristinare questi componenti dal backup di configurazione. 

## <a name="vmware-software-versions"></a>Versioni del software VMware
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati trattati i processi e le funzionalità di aggiornamento chiave in soluzione Azure VMware, è possibile ottenere informazioni su:

- [Come creare un cloud privato](tutorial-create-private-cloud.md)
- [Come abilitare soluzione Azure VMware risorsa](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
