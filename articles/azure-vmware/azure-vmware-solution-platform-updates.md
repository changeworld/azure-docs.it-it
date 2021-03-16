---
title: Aggiornamenti della piattaforma per la soluzione VMware di Azure
description: Informazioni sugli aggiornamenti della piattaforma per la soluzione VMware di Azure.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 4f4c697f345cca093a83eab2f915aaf9e80ab10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563130"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aggiornamenti della piattaforma per la soluzione VMware di Azure

Gli aggiornamenti importanti della soluzione VMware di Azure verranno applicati a partire dal 2021 marzo. Si riceverà una notifica tramite integrità dei servizi di Azure che include la sequenza temporale della manutenzione. In questo articolo si apprenderà cosa aspettarsi durante questa operazione di manutenzione e le modifiche apportate al cloud privato.

## <a name="march-15-2021"></a>15 marzo 2021 

- Il servizio di soluzione VMware di Azure eseguirà operazioni di manutenzione per aggiornare il server vCenter nel cloud privato a server vCenter 6,7 aggiornamento 3L versione fino al 19 marzo 2021.

- Durante questo periodo, VMware vCenter non sarà disponibile e non sarà possibile gestire le macchine virtuali (arresto, avvio, creazione, eliminazione). La disponibilità elevata VMware continuerà a funzionare per fornire protezione per le macchine virtuali esistenti. Anche la scalabilità del cloud privato (aggiunta/rimozione di server e cluster) non sarà disponibile.
 
Per altre informazioni su questa versione di vCenter, vedere [server VMware vCenter 6,7 aggiornamento 3L note](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)sulla versione.

## <a name="march-4-2021"></a>4 marzo 2021

- Le soluzioni VMware di Azure applicano le patch a ESXi nei cloud privati esistenti a [VMware ESXi 6,7, Patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) fino al 15 marzo 2021.

- Le soluzioni alternative documentate per lo stack vSphere, come per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), verranno applicate anche fino al 15 marzo 2021.

>[!NOTE]
>Questa operazione non è problematica e non dovrebbe influire sui carichi di lavoro o sui servizi VMware di Azure. Durante la manutenzione, diversi avvisi VMware, ad esempio la _perdita della connettività di rete in DVPorts_ e la _perdita di ridondanza uplink in DVPorts_, vengono visualizzati in vCenter e cancellati automaticamente durante l'avanzamento della manutenzione.

## <a name="post-update"></a>Post-aggiornamento
Al termine, vengono visualizzate le versioni più recenti dei componenti VMware. Se si riscontrano problemi o si verificano domande, contattare il team di supporto aprendo un ticket di supporto.



