---
title: Domande frequenti su Windows Virtual Desktop Start VM Connect-Azure
description: Domande frequenti e procedure consigliate per l'uso della funzionalità Avvia macchina virtuale in connessione.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0a35d0541358af2f5fac5e4c7486a1be93797922
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445585"
---
# <a name="start-vm-on-connect-faq-preview"></a>Domande frequenti sull'avvio della macchina virtuale in Connect (anteprima)

> [!IMPORTANT]
> La funzionalità avvia VM on Connect è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo descrive le domande frequenti sulla funzionalità di avvio della macchina virtuale (VM) in connessione (anteprima) per i pool di host di desktop virtuali Windows.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Le macchine virtuali vengono deallocate automaticamente quando un utente smette di usarle?

No. È necessario configurare criteri aggiuntivi per consentire agli utenti di uscire dalle proprie sessioni ed eseguire gli script di automazione di Azure per deallocare le macchine virtuali.

Per configurare i criteri di deallocazione:

1. Connettersi in remoto alla macchina virtuale per cui si vuole impostare il criterio.

2. Aprire l' **Editor criteri di gruppo**, quindi passare a **criteri del computer locale**  >  **Configurazione computer**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **Servizi Desktop remoto**  >  **host sessione Desktop remoto**  >  **limiti di tempo della sessione**.

3. Trovare i criteri **per impostare il limite di tempo per le sessioni disconnesse**, quindi impostare il valore su **abilitato**.

4. Dopo aver abilitato il criterio, selezionare **Termina una sessione disconnessa**.

>[!NOTE]
>Assicurarsi di impostare il limite di tempo per il criterio "termina una sessione disconnessa" su un valore maggiore di cinque minuti. Un limite di tempo ridotto può causare la chiusura delle sessioni degli utenti se la rete perde la connessione per troppo tempo, causando un lavoro perso.

Per la firma degli utenti non verranno deallocate le VM. Per informazioni su come deallocare le macchine virtuali, vedere [avviare o arrestare VM durante le ore](../automation/automation-solution-vm-management.md)di indisponibilità.

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Gli utenti possono disattivare la macchina virtuale dai client?

Sì. Gli utenti possono arrestare la macchina virtuale usando il menu Start all'interno della sessione, proprio come se si trattasse di un computer fisico. Tuttavia, se si arresta la macchina virtuale, la macchina virtuale non verrà deallocata. Per informazioni su come deallocare le macchine virtuali, vedere [avviare o arrestare VM durante le ore](../automation/automation-solution-vm-management.md)di indisponibilità.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'avvio di una VM in Connect, vedere [avviare una macchina virtuale in Connect (anteprima)](start-virtual-machine-connect.md).

Per altre domande generali sul desktop virtuale di Windows, vedere le domande [frequenti](faq.md)generali.
