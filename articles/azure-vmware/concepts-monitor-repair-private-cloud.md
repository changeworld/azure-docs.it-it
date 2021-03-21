---
title: Concetti-monitoraggio e ripristino di cloud privati della soluzione VMware di Azure
description: Scopri in che modo la soluzione VMware di Azure monitora e ripristina VMware ESXi Server in un cloud privato della soluzione VMware di Azure.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633139"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorare e ripristinare i cloud privati della soluzione VMware di Azure

La soluzione VMware di Azure monitora costantemente i server VMware ESXi in un cloud privato della soluzione VMware di Azure. 

## <a name="what-azure-vmware-solution-monitors"></a>Quali monitoraggi della soluzione VMware di Azure

La soluzione VMware di Azure monitora le condizioni seguenti nell'host:  

- Stato processore 
- Stato memoria 
- Stato di connessione e di alimentazione 
- Stato della ventola hardware 
- Perdita di connettività di rete 
- Stato della lavagna del sistema hardware 
- Si sono verificati errori sui dischi di un host rete VSAN 
- Tensione hardware 
- Stato temperatura hardware 
- Stato di alimentazione hardware 
- Stato di archiviazione 
- Errore di connessione 

> [!NOTE]
> Gli amministratori tenant della soluzione VMware di Azure non devono modificare o eliminare gli avvisi di VMware vCenter definiti sopra, perché sono gestiti dal piano di controllo della soluzione VMware di Azure in vCenter. Questi avvisi vengono usati dal monitoraggio della soluzione VMware di Azure per attivare il processo di correzione dell'host della soluzione VMware di Azure.

## <a name="azure-vmware-solution-host-remediation"></a>Monitoraggio e aggiornamento dell'host della soluzione VMware di Azure  

Quando la soluzione VMware di Azure rileva un calo o un errore in un nodo della soluzione VMware di Azure, attiva il processo di monitoraggio e aggiornamento dell'host. La correzione host comporta la sostituzione del nodo difettoso con un nuovo nodo integro.  

La correzione dell'host inizia con l'aggiunta di un nuovo nodo integro nel cluster. Quindi, quando possibile, l'host difettoso viene inserito in modalità di manutenzione VMware vSphere. VMware vMotion sposta le VM dall'host difettoso ad altri server disponibili nel cluster, consentendo potenzialmente un tempo di inattività per la migrazione in tempo reale dei carichi di lavoro. Se l'host difettoso non può essere inserito in modalità di manutenzione, l'host viene rimosso dal cluster.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato illustrato il modo in cui la soluzione VMware di Azure monitora e ripristina i cloud privati, è possibile ottenere informazioni su:

- [Aggiornamenti del cloud privato della soluzione VMware di Azure](concepts-upgrades.md).
- [Come abilitare la risorsa della soluzione VMware di Azure](enable-azure-vmware-solution.md).
