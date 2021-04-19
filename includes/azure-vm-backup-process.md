---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716749"
---
1. Per le macchine virtuali di Azure selezionate per il backup, Backup di Azure avvia un processo di backup in base alla pianificazione di backup specificata.
1. Durante il primo backup viene installata un'estensione di backup nella macchina virtuale se la macchina virtuale è in esecuzione.
    - Per le macchine virtuali Windows, è [installata l'estensione VMSnapshot.](../articles/virtual-machines/extensions/vmsnapshot-windows.md)
    - Per le macchine virtuali Linux, viene [installata l'estensione VMSnapshotLinux.](../articles/virtual-machines/extensions/vmsnapshot-linux.md)
1. Per le macchine virtuali Windows in esecuzione, Backup si coordina con Windows Servizio Copia Shadow del volume (VSS) per creare uno snapshot coerente con l'app della macchina virtuale.
    - Per impostazione predefinita, Backup accetta backup VSS completi.
    - Se Backup non può creare uno snapshot coerente con l'app, viene creato uno snapshot coerente con i file dell'archiviazione sottostante, perché non si verificano scritture dell'applicazione durante l'arresto della macchina virtuale.
1. Per le macchine virtuali Linux, Backup richiede un backup coerente con i file. Per gli snapshot coerenti con l'app, è necessario personalizzare manualmente gli script di pre/post-
1. Dopo che Backup ha creato lo snapshot, trasferisce i dati nell'insieme di credenziali.
    - Il backup viene ottimizzato eseguendo il backup di ogni disco della macchina virtuale in parallelo.
    - Per ogni disco di cui viene eseguito il backup, Backup di Azure legge i blocchi sul disco e identifica e trasferisce solo i blocchi di dati modificati (il delta) rispetto al backup precedente.
    - È possibile che i dati dello snapshot non vengano copiati immediatamente nell'insieme di credenziali. Questa operazione potrebbe richiedere alcune ore nei momenti di picco. Il tempo totale di backup per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
1. Le modifiche apportate a una macchina virtuale Windows dopo Backup di Azure abilitata in essa sono:
    - Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 è installato nella macchina virtuale
    - Tipo di avvio del servizio Copia Shadow del volume (VSS) modificato in automatico da manuale
    - Aggiunta del servizio Windows IaaSVmProvider

1. Al termine del trasferimento dei dati, lo snapshot viene rimosso e viene creato un punto di ripristino.

![Architettura del backup delle macchine virtuali di Azure](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
