---
title: Aggiornamenti della piattaforma per la soluzione VMware di Azure
description: Informazioni sugli aggiornamenti della piattaforma per la soluzione VMware di Azure.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045213"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aggiornamenti della piattaforma per la soluzione VMware di Azure

La soluzione VMware di Azure applicherà aggiornamenti importanti a partire dal 2021 marzo. Si riceverà una notifica tramite integrità dei servizi di Azure che include la sequenza temporale della manutenzione. Per altre informazioni, vedere [aggiornamenti e aggiornamenti del cloud privato della soluzione VMware di Azure](concepts-upgrades.md).

## <a name="march-24-2021"></a>24 marzo, 2021
Tutti i nuovi cloud privati della soluzione VMware di Azure vengono distribuiti con VMware vCenter versione 6.7 U3l e NSX-T versione 3.1.1. Tutti i cloud privati esistenti verranno aggiornati e aggiornati **fino al 2021 giugno** alle versioni indicate in precedenza.

Si riceverà un messaggio di posta elettronica con la data e l'ora di manutenzione pianificata. È possibile ripianificare un aggiornamento. Il messaggio di posta elettronica fornisce anche i dettagli sul componente aggiornato, l'effetto sui carichi di lavoro, l'accesso al cloud privato e altri servizi di Azure.  Un'ora prima dell'aggiornamento, si riceverà una notifica e quindi di nuovo al termine dell'operazione.

## <a name="march-15-2021"></a>15 marzo 2021 

- Il servizio di soluzione VMware di Azure eseguirà operazioni di manutenzione **fino al 19 marzo 2021** per aggiornare il server vCenter nel cloud privato alla versione server vCenter 6,7 Update 3L.

- VMware vCenter non sarà disponibile in questo periodo di tempo.  Quindi, non sarà possibile gestire le macchine virtuali (arresto, avvio, creazione, eliminazione) o scalabilità del cloud privato (aggiunta/rimozione di server e cluster). Tuttavia, la disponibilità elevata di VMware continuerà a funzionare per proteggere le macchine virtuali esistenti. 
 
Per altre informazioni su questa versione di vCenter, vedere [server VMware vCenter 6,7 aggiornamento 3L note](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)sulla versione.

## <a name="march-4-2021"></a>4 marzo 2021

- La soluzione VMware di Azure applicherà la [VMware ESXi 6,7, Patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) ai privati esistenti **fino al 15 marzo 2021**.

- Le soluzioni alternative documentate per lo stack vSphere, come per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), verranno applicate anche **fino al 15 marzo 2021**.

>[!NOTE]
>Questa operazione non è problematica e non dovrebbe influire sui carichi di lavoro o sui servizi VMware di Azure. Durante la manutenzione, diversi avvisi VMware, ad esempio la _perdita della connettività di rete in DVPorts_ e la _perdita di ridondanza uplink in DVPorts_, vengono visualizzati in vCenter e cancellati automaticamente durante l'avanzamento della manutenzione.

## <a name="post-update"></a>Post-aggiornamento
Al termine, vengono visualizzate le versioni più recenti dei componenti VMware. Se si riscontrano problemi o si verificano domande, contattare il team di supporto aprendo un ticket di supporto.