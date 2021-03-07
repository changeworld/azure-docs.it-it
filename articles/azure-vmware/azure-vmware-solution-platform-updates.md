---
title: Aggiornamenti della piattaforma per la soluzione VMware di Azure
description: Informazioni sugli aggiornamenti della piattaforma per la soluzione VMware di Azure.
ms.topic: reference
ms.date: 03/05/2021
ms.openlocfilehash: 1f1a0c29ffde20b54abb9e4d1d1127fc93a712d9
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444053"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Aggiornamenti della piattaforma per la soluzione VMware di Azure


## <a name="march-4-2021"></a>4 marzo 2021

Gli aggiornamenti importanti per le soluzioni VMware di Azure verranno applicati a partire dal 2021 marzo. Si riceverà una notifica tramite integrità dei servizi di Azure che include la sequenza temporale della manutenzione. In questo articolo si apprenderà cosa aspettarsi durante questa operazione di manutenzione e le modifiche apportate al cloud privato.

- Le soluzioni VMware di Azure applicano le patch a ESXi nei cloud privati esistenti a [VMware ESXi 6,7, Patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) fino al 15 marzo 2021.

- Le soluzioni alternative documentate per lo stack vSphere, come per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), verranno applicate anche fino al 15 marzo 2021.

>[!NOTE]
>Questa operazione non è problematica e non dovrebbe influire sui carichi di lavoro o sui servizi VMware di Azure. Durante la manutenzione, diversi avvisi VMware, ad esempio la _perdita della connettività di rete in DVPorts_ e la _perdita di ridondanza uplink in DVPorts_, vengono visualizzati in vCenter e cancellati automaticamente durante l'avanzamento della manutenzione.


## <a name="post-update"></a>Post-aggiornamento
Al termine, vengono visualizzate le versioni più recenti dei componenti VMware. Se si riscontrano problemi o si verificano domande, contattare il team di supporto aprendo un ticket di supporto.



