---
title: 'Concetti: identità e accesso'
description: Informazioni sui concetti di identità e accesso della soluzione VMware di Azure
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 07a7ac8093524ef4240b8f7607d649520b9439e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586251"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concetti di identità della soluzione VMware di Azure

Viene eseguito il provisioning dei cloud privati della soluzione VMware di Azure con un server vCenter e una gestione NSX-T. VCenter viene usato per gestire i carichi di lavoro delle macchine virtuali (VM). Per gestire ed estendere la rete cloud privata, è possibile usare la gestione NSX-T.

La gestione delle identità e dell'accesso a vCenter usa i privilegi del gruppo buildin CloudAdmin. Il gestore NSX-T USA autorizzazioni di amministratore limitate. Questo è per natura del servizio gestito e garantisce che la piattaforma cloud privata venga aggiornata con le funzionalità e le patch più recenti previste.  Per altre informazioni, vedere l' [articolo Concetti relativi agli aggiornamenti per il cloud privato][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>accesso e identità vCenter

Il gruppo vCenter CloudAdmin definisce e fornisce i privilegi in vCenter. Un'altra opzione consiste nel fornire l'accesso e l'identità tramite l'integrazione di vCenter LDAP Single Sign-On con Azure Active Directory. Questa integrazione viene abilitata dopo la distribuzione del cloud privato. 

La tabella mostra i privilegi **CloudAdmin** e **CloudGlobalAdmin** .

|  Set di privilegi           | CloudAdmin | CloudGlobalAdmin | Commento |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Avvisi                  | Un utente CloudAdmin dispone di tutti i privilegi di allarme per gli avvisi nel Compute-ResourcePool e nelle macchine virtuali.     |          --        |  -- |
|  Distribuzione automatica             |  --  |        --        |  Microsoft esegue la gestione degli host.  |
|  Certificati            |  --  |        --       |  Microsoft esegue la gestione dei certificati.  |
|  Raccolta contenuto         | Un utente CloudAdmin dispone dei privilegi necessari per creare e usare i file in una raccolta contenuto.    |         Abilitato con SSO.         |  Microsoft distribuirà i file nella raccolta contenuto agli host ESXi.  |
|  Data center              |  --  |        --          |  Microsoft esegue tutte le operazioni data center.  |
|  Archivio dati               | Datastore. AllocateSpace, datastore. browse, Datastore.Config, datastore. DeleteFile, datastore. filemanagement, datastore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Agent Manager ESX       |  --  |         --       |  Microsoft esegue tutte le operazioni.  |
|  Cartella                  |  Un utente CloudAdmin dispone di tutti i privilegi di cartella.     |  --  |  --  |
|  Globale                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host. hbr. HbrManagement      |        --          |  Microsoft esegue tutte le altre operazioni host.  |
|  InventoryService        |  InventoryService. tagging      |        --          |  --  |
|  Rete                 |  Network.Assign    |                  |  Microsoft esegue tutte le altre operazioni di rete.  |
|  Autorizzazioni             |  --  |        --       |  Microsoft esegue tutte le operazioni di autorizzazione.  |
|  Archiviazione basata su profili  |  --  |        --       |  Microsoft esegue tutte le operazioni di profilo.  |
|  Risorsa                |  Un utente CloudAdmin dispone di tutti i privilegi delle risorse.        |      --       | --   |
|  Attività pianificata.          |  Un utente CloudAdmin ha tutti i privilegi di ScheduleTask.   |   --   | -- |
|  Sessioni                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  Microsoft esegue tutte le altre operazioni di sessione.  |
|  Viste di archiviazione           |  StorageViews. View   |        --          |  Microsoft esegue tutte le altre operazioni di visualizzazione dell'archiviazione (Configura servizio).  |
|  Attività                   |  --  |  --   |  Microsoft gestisce le estensioni per la gestione delle attività.  |
|  vApp                    |  Un utente CloudAdmin ha tutti i privilegi di vApp.  |  --  |  --  |
|  Macchina virtuale         |  Un utente CloudAdmin ha tutti i privilegi di VirtualMachine.  |  --  |  --  |
|  vService                |  Un utente CloudAdmin ha tutti i privilegi di vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Accesso e identità di gestione NSX-T

Usare l'account *Administrator* per accedere a NSX-T Manager. Dispone di privilegi completi e consente di creare e gestire gateway di livello 1 (T1), segmenti (commutatori logici) e tutti i servizi. Questo account fornisce anche l'accesso al gateway NSX-T Tier-0 (T0). Tenere in considerazione le modifiche apportate, poiché ciò potrebbe causare una riduzione delle prestazioni della rete o l'accesso al cloud privato. Aprire una richiesta di supporto nella portale di Azure per richiedere eventuali modifiche al gateway NSX-T T0.
  
## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati trattati i concetti relativi all'accesso e all'identità della soluzione VMware di Azure, è possibile ottenere informazioni su:

- [Concetti sull'aggiornamento del cloud privato](concepts-upgrades.md).
- [controllo degli accessi in base al ruolo vSphere per la soluzione VMware di Azure](concepts-role-based-access-control.md).
- [Come abilitare la risorsa della soluzione VMware di Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
