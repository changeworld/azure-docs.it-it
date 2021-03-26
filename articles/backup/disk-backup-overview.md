---
title: Panoramica di backup su disco di Azure
description: Informazioni sulla soluzione di backup su disco di Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 9449fdc57909cb143d381ae074913c79d24c8893
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107296"
---
# <a name="overview-of-azure-disk-backup"></a>Panoramica di backup su disco di Azure

Il backup su disco di Azure è una soluzione di backup nativa basata sul cloud che protegge i dati nei dischi gestiti. Si tratta di una soluzione semplice, sicura ed economica che consente di configurare la protezione per i dischi gestiti in pochi passaggi. Garantisce che sia possibile ripristinare i dati in uno scenario di emergenza.

Il backup su disco di Azure offre una soluzione chiavi in mano che fornisce la gestione del ciclo di vita degli snapshot per i dischi gestiti automatizzando la creazione periodica di snapshot e la conservazione per la durata configurata tramite criteri di backup. È possibile gestire gli snapshot del disco con costi di infrastruttura zero e senza la necessità di generare script personalizzati o di qualsiasi sovraccarico di gestione. Si tratta di una soluzione di backup coerente con l'arresto anomalo del sistema che esegue un backup temporizzato di un disco gestito utilizzando [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md) con supporto per più backup al giorno. È anche una soluzione senza agente e non influisca sulle prestazioni dell'applicazione di produzione. Supporta il backup e il ripristino dei dischi del sistema operativo e dei dati (inclusi i dischi condivisi), indipendentemente dal fatto che siano attualmente collegati a una macchina virtuale di Azure in esecuzione.

Se è necessario eseguire il backup coerente con l'applicazione della macchina virtuale, inclusi i dischi dati, oppure un'opzione per ripristinare un'intera macchina virtuale dal backup, ripristinare un file o una cartella o eseguire il ripristino in un'area secondaria, usare la soluzione di [backup delle VM di Azure](backup-azure-vms-introduction.md) . Backup di Azure offre il supporto affiancato per il backup di dischi gestiti usando il backup su disco, oltre alle soluzioni di [backup delle VM di Azure](./backup-azure-vms-introduction.md) . Questa operazione è utile quando sono necessari backup coerenti con le applicazioni una volta al giorno per le macchine virtuali e anche backup più frequenti dei dischi del sistema operativo o di un disco dati specifico che sono coerenti con l'arresto anomalo del sistema e che non influiscano sulle prestazioni dell'applicazione di produzione.

Il backup su disco di Azure è integrato nel centro di backup, che fornisce una **singola esperienza di gestione unificata** in Azure per le aziende per la gestione, il monitoraggio, il funzionamento e l'analisi dei backup su larga scala.

## <a name="key-benefits-of-disk-backup"></a>Vantaggi principali del backup su disco

Il backup su disco di Azure è una soluzione coerente senza agenti e di arresto anomalo del sistema che usa [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md) e offre i vantaggi seguenti:

- Backup più frequenti e veloci senza interrompere la macchina virtuale.
- Non influisce sulle prestazioni dell'applicazione di produzione.
- Nessun problema di sicurezza perché non richiede l'esecuzione di script personalizzati o l'installazione di agenti.
- Soluzione economica per eseguire il backup di dischi specifici rispetto al backup di un'intera macchina virtuale.

La soluzione di backup su disco di Azure è utile negli scenari seguenti:

- Necessità di backup frequenti al giorno senza l'applicazione in stato di riposo
- App in esecuzione in uno scenario di cluster: i cluster di failover di Windows Server e Linux scrivono in dischi condivisi
- Necessità specifica del backup senza agenti a causa di problemi di sicurezza o di prestazioni dell'applicazione
- Il backup coerente con l'applicazione della macchina virtuale non è possibile perché le app line-of-business non supportano Servizio Copia Shadow del volume (VSS).

Prendere in considerazione il backup del disco di Azure negli scenari in cui:

- un'applicazione mission-critical viene eseguita in una macchina virtuale di Azure che richiede più backup al giorno per soddisfare l'obiettivo del punto di ripristino, ma senza alcun impatto sull'ambiente di produzione o sulle prestazioni dell'applicazione
- il regolamento dell'organizzazione o del settore limita l'installazione degli agenti a causa di problemi di sicurezza
- l'esecuzione di script pre o post personalizzati e la chiamata di Freeze e scongela in macchine virtuali Linux per ottenere un backup coerente con l'applicazione comporta un sovraccarico eccessivo della disponibilità del carico di lavoro di produzione
- le applicazioni incluse in contenitori in esecuzione nel servizio Azure Kubernetes (cluster AKS) usano dischi gestiti come archivio permanente. Oggi è necessario eseguire il backup del disco gestito tramite gli script di automazione difficili da gestire.
- un disco gestito contiene dati aziendali critici, usati come una condivisione di file o file di backup del database e si desidera ottimizzare i costi di backup senza investire nel backup delle macchine virtuali di Azure
- Sono presenti molte macchine virtuali Linux e Windows su disco singolo (ovvero una macchina virtuale con un solo disco del sistema operativo e nessun disco dati collegato) che ospitano computer WebServer o senza stato o funge da ambiente di gestione temporanea con le impostazioni di configurazione dell'applicazione ed è necessaria una soluzione di backup conveniente per proteggere il disco del sistema operativo. Ad esempio, per attivare un backup su richiesta rapido prima di eseguire l'aggiornamento o l'applicazione di patch alla macchina virtuale
- una macchina virtuale esegue una configurazione del sistema operativo non supportata dalla soluzione di backup delle VM di Azure (ad esempio, server Windows a 2008 32 bit)

## <a name="how-the-backup-and-restore-process-works"></a>Funzionamento del processo di backup e ripristino

- Il primo passaggio per la configurazione del backup per Azure Managed disks consiste nel creare un insieme di credenziali di [backup](backup-vault-overview.md). L'insieme di credenziali offre una visualizzazione consolidata dei backup configurati in carichi di lavoro diversi.

- Creare quindi un criterio di backup che consenta di configurare la frequenza di backup e la durata di conservazione.

- Per configurare il backup, passare all'insieme di credenziali per il backup, assegnare un criterio di backup, selezionare il disco gestito di cui deve essere eseguito il backup e fornire un gruppo di risorse in cui archiviare e gestire gli snapshot. Backup di Azure attiva automaticamente i processi di backup pianificati che creano uno snapshot incrementale del disco in base alla frequenza di backup. Gli snapshot meno recenti vengono eliminati in base alla durata di conservazione specificata dal criterio di backup.

- Backup di Azure usa [snapshot incrementali](../virtual-machines/disks-incremental-snapshots.md#restrictions) del disco gestito. Gli snapshot incrementali rappresentano un backup temporizzato e conveniente di dischi gestiti fatturati per le modifiche delta apportate al disco dall'ultimo snapshot. Vengono sempre archiviati nella risorsa di archiviazione più economica, con archiviazione HDD standard indipendentemente dal tipo di archiviazione dei dischi padre. Il primo snapshot del disco occuperà le dimensioni usate del disco e gli snapshot incrementali consecutivi archiviano le modifiche delta sul disco dall'ultimo snapshot.

- Dopo aver configurato il backup di un disco gestito, verrà creata un'istanza di backup all'interno dell'insieme di credenziali per il backup. Utilizzando l'istanza di backup, è possibile trovare l'integrità delle operazioni di backup, attivare i backup su richiesta ed eseguire operazioni di ripristino. È anche possibile visualizzare l'integrità dei backup tra più insiemi di credenziali e istanze di backup usando il centro di backup, che fornisce un unico riquadro di visualizzazione a cristalli.

- Per eseguire il ripristino, è sufficiente selezionare il punto di ripristino da cui si desidera ripristinare il disco. Specificare il gruppo di risorse in cui deve essere creato il disco ripristinato dallo snapshot. Backup di Azure offre un'esperienza di ripristino immediato, perché gli snapshot vengono archiviati localmente nella sottoscrizione.

- L'insieme di credenziali di backup usa l'identità gestita per accedere ad altre risorse di Azure. Per configurare il backup di un disco gestito e per eseguire il ripristino dal backup precedente, l'identità gestita dell'insieme di credenziali di backup richiede un set di autorizzazioni sul disco di origine, il gruppo di risorse snapshot in cui vengono creati e gestiti gli snapshot e il gruppo di risorse di destinazione in cui si desidera ripristinare il backup. È possibile concedere le autorizzazioni all'identità gestita usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Identità gestita è un'entità servizio di un tipo speciale che può essere usata solo con le risorse di Azure. Altre informazioni sulle [identità gestite](../active-directory/managed-identities-azure-resources/overview.md).

- Attualmente backup su disco di Azure supporta il backup operativo di dischi gestiti e non copia i backup nell'archivio dell'insieme di credenziali di backup. Per un elenco dettagliato degli scenari supportati e non supportati e della disponibilità delle aree, vedere la [matrice di supporto](disk-backup-support-matrix.md).

## <a name="pricing"></a>Prezzi

Backup di Azure offre una soluzione di gestione del ciclo di vita degli snapshot per proteggere i dischi di Azure. Gli snapshot del disco creati da backup di Azure vengono archiviati nel gruppo di risorse all'interno della sottoscrizione di Azure e comportano addebiti per l' **archiviazione di snapshot** . Per informazioni dettagliate sui prezzi degli snapshot, vedere [prezzi dei dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/) . Poiché gli snapshot non vengono copiati nell'insieme di credenziali per il backup, backup di Azure non addebita una tariffa per l' **istanza protetta** e il costo di **archiviazione dei backup** non è applicabile. Gli snapshot incrementali occupano inoltre modifiche differenziali dall'ultimo snapshot e vengono sempre archiviati nell'archiviazione standard indipendentemente dal tipo di archiviazione dei dischi gestiti dall'elemento padre e vengono addebitati in base ai prezzi dell'archiviazione standard. In questo modo il backup dei dischi di Azure è una soluzione conveniente.

## <a name="next-steps"></a>Passaggi successivi

- [Matrice di supporto per il backup di dischi di Azure](disk-backup-support-matrix.md)
