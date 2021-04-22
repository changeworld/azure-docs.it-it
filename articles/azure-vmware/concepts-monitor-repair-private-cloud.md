---
title: Concetti - Monitorare e ripristinare soluzione Azure VMware cloud privati
description: Informazioni su soluzione Azure VMware monitoraggio e ripristino VMware ESXi server in un soluzione Azure VMware cloud privato.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 9fa94d6093e03432f20672ecf5c0160ce479e175
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871670"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorare e ripristinare soluzione Azure VMware cloud privati

soluzione Azure VMware monitora continuamente i server VMware ESXi in un soluzione Azure VMware cloud privato. 

## <a name="what-azure-vmware-solution-monitors"></a>Quali soluzione Azure VMware monitoraggi

soluzione Azure VMware monitora le condizioni seguenti nell'host:  

- Stato del processore 
- Stato della memoria 
- Stato di connessione e alimentazione 
- Stato ventola hardware 
- Perdita di connettività di rete 
- Stato della scheda del sistema hardware 
- Si sono verificati errori nei dischi di un host vSAN 
- Tensione hardware 
- Stato della temperatura hardware 
- Stato di alimentazione dell'hardware 
- Stato dell'archiviazione 
- Errore di connessione 

> [!NOTE]
> soluzione Azure VMware gli amministratori del tenant non devono modificare o eliminare gli avvisi VMware vCenter definiti in precedenza, poiché sono gestiti dal piano di controllo soluzione Azure VMware in vCenter. Questi allarmi vengono usati dal monitoraggio soluzione Azure VMware per attivare il soluzione Azure VMware di correzione dell'host.

## <a name="azure-vmware-solution-host-remediation"></a>soluzione Azure VMware monitoraggio e aggiornamento dell'host  

Quando soluzione Azure VMware rileva una riduzione o un errore in un nodo soluzione Azure VMware, attiva il processo di correzione dell'host. La correzione dell'host comporta la sostituzione del nodo difettoso con un nuovo nodo integro.  

La correzione host inizia aggiungendo un nuovo nodo integro nel cluster. Quando possibile, l'host difettoso viene quindi inserito in VMware vSphere di manutenzione. VMware vMotion sposta le macchine virtuali dall'host difettoso ad altri server disponibili nel cluster, consentendo potenzialmente tempi di inattività pari a zero per la migrazione in tempo reale dei carichi di lavoro. Se l'host difettoso non può essere inserito in modalità di manutenzione, l'host viene rimosso dal cluster.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato illustrato soluzione Azure VMware e ripara i cloud privati, è possibile ottenere informazioni su:

- [soluzione Azure VMware aggiornamenti del cloud privato](concepts-upgrades.md)
- [Come abilitare soluzione Azure VMware risorsa](enable-azure-vmware-solution.md)
