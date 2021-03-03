---
title: Configurare i criteri di esportazione per il volume Azure NetApp Files NFS
description: Viene descritto come configurare i criteri di esportazione per controllare l'accesso a un volume NFS utilizzando Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: d3f268a4ce2660350055367770e987a06828e2d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740120"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurare i criteri di esportazione per un volume NFS

È possibile configurare i criteri di esportazione per controllare l'accesso a un volume Azure NetApp Files. Azure NetApp Files i criteri di esportazione supportano i volumi che usano il protocollo NFS (NFSv3 e NFSv 4.1) e il protocollo Dual (NFSv3 e SMB). 

È possibile creare fino a cinque regole dei criteri di esportazione.

## <a name="configure-the-policy"></a>Configurare i criteri 

1.  Nella pagina **volumi** selezionare il volume per il quale si desidera configurare i criteri di esportazione, quindi selezionare **Esporta criteri**. È anche possibile configurare i criteri di esportazione durante la creazione del volume.

2.  Per creare una regola di criteri di esportazione, specificare le informazioni seguenti:   
    * **Index**: specificare il numero di indice per la regola.  
      
      I criteri di esportazione possono essere costituiti da un massimo di cinque regole. Le regole vengono valutate in base al relativo ordine nell'elenco dei numeri di indice. Le regole con numeri di indice inferiori vengono valutate per prime. Ad esempio, la regola con numero di indice 1 viene valutata prima della regola con numero di indice 2. 

    * **Client consentiti**: specificare il valore in uno dei formati seguenti:  
      * Indirizzo IPv4. Esempio: `10.1.12.24`
      * Indirizzo IPv4 con subnet mask espresso come numero di bit. Esempio: `10.1.12.10/4`
      * Indirizzi IP separati da virgole. È possibile immettere più indirizzi IP host in una singola regola separandoli con virgole. Esempio: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Accesso**: selezionare uno dei tipi di accesso seguenti:  
      * Nessun accesso 
      * Lettura e scrittura
      * Sola lettura

    * Di sola **lettura** e di **lettura/scrittura**: se si usa la crittografia Kerberos con NFSv 4.1, seguire le istruzioni riportate in [configurare NFSv 4.1 crittografia Kerberos](configure-kerberos-encryption.md).  Per l'effetto sulle prestazioni di Kerberos, vedere l' [effetto sulle prestazioni di Kerberos nei volumi NFSv 4.1](performance-impact-kerberos.md). 

      ![Opzioni di sicurezza Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Accesso radice**: specificare se l' `root` account può accedere al volume.  Per impostazione predefinita, l'accesso alla radice è impostato **su on** e l' `root` account ha accesso al volume.

      ![Criteri di esportazione](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Passaggi successivi 
* [Montare o smontare un volume per le macchine virtuali](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gestire gli snapshot](azure-netapp-files-manage-snapshots.md)
