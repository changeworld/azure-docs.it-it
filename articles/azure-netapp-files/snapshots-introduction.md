---
title: Come funzionano gli snapshot Azure NetApp Files | Microsoft Docs
description: Illustra il funzionamento di Azure NetApp Files snapshot, inclusi i modi per creare snapshot, modi per ripristinare gli snapshot, come usare gli snapshot nelle impostazioni di replica tra più aree.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: b-juche
ms.openlocfilehash: 4d21f7c4e74a87e409a73b22fc6b316e97e24a4e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122366"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Funzionamento degli snapshot Azure NetApp Files

Questo articolo illustra il funzionamento di Azure NetApp Files snapshot. Azure NetApp Files tecnologia snapshot offre stabilità, scalabilità e recuperabilità più veloce, senza alcun effetto sulle prestazioni. Azure NetApp Files tecnologia snapshot fornisce le basi per le soluzioni di protezione dei dati, inclusi i ripristini di file singoli, i ripristini di volumi e i cloni e la replica tra aree. 

Per i passaggi relativi all'uso degli snapshot del volume, vedere [gestire gli snapshot con Azure NetApp files](azure-netapp-files-manage-snapshots.md). Per considerazioni sulla gestione degli snapshot nella replica tra aree, vedere [requisiti e considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Quali snapshot del volume  

Uno snapshot Azure NetApp Files è un'immagine di file system (volume) temporizzata. Può fungere da backup online ideale. È possibile utilizzare uno snapshot per [creare un nuovo volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [ripristinare un file](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)o [ripristinare un volume](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). Nei dati specifici dell'applicazione archiviati in Azure NetApp Files volumi, potrebbe essere necessario eseguire passaggi aggiuntivi per garantire la coerenza dell'applicazione.  

Gli snapshot con overhead ridotto sono resi possibili dalle funzionalità esclusive della tecnologia di virtualizzazione del volume che fa parte di Azure NetApp Files. Analogamente a un database, questo livello utilizza i puntatori ai blocchi di dati effettivi sul disco. Tuttavia, a differenza di un database, non riscrive i blocchi esistenti; scrive i dati aggiornati in un nuovo blocco e modifica il puntatore. Uno snapshot di Azure NetApp Files modifica semplicemente i puntatori di blocco, creando una visualizzazione "bloccata" di sola lettura di un volume che consente alle applicazioni di accedere alle versioni precedenti dei file e delle gerarchie di directory senza programmazione speciale. I blocchi di dati effettivi non vengono copiati. Di conseguenza, gli snapshot sono efficienti nel tempo necessario per crearli; sono quasi istantanee, indipendentemente dalle dimensioni del volume. Gli snapshot sono inoltre efficienti nello spazio di archiviazione. Non utilizzano alcuno spazio e vengono conservati solo i blocchi Delta tra gli snapshot e il volume attivo. 

I diagrammi seguenti illustrano i concetti:  

![Diagrammi che mostrano i concetti chiave degli snapshot](../media/azure-netapp-files/snapshot-concepts.png)

Nei diagrammi precedenti, lo snapshot è riportato nella figura 1a. Nella Figura 1B i dati modificati vengono scritti in un *nuovo blocco* e il puntatore viene aggiornato. Tuttavia, il puntatore dello snapshot fa ancora riferimento al *blocco scritto in precedenza*, offrendo una visualizzazione dinamica dei dati. Un altro snapshot viene assunto nella figura 1c. A questo punto è possibile accedere a tre generazioni di dati (dati Live, snapshot 2 e snapshot 1, in ordine di età), senza occupare lo spazio del volume necessario per tre copie complete. 

Uno snapshot accetta solo una copia dei metadati del volume (*tabella inode*). La creazione di, indipendentemente dalle dimensioni del volume, dalla capacità utilizzata o dal livello di attività del volume, richiede solo pochi secondi. Quindi, la creazione di uno snapshot di un volume 100-TiB accetta lo stesso tempo (accanto a zero) per la creazione di uno snapshot di un volume 100-GiB. Dopo la creazione di uno snapshot, le modifiche apportate ai file di dati vengono riflesse nella versione attiva dei file, come di consueto.

Nel frattempo, i blocchi di dati a cui punta uno snapshot rimangono stabili e non modificabili. A causa della natura di "Reindirizzamento in scrittura" degli snapshot Azure NetApp Files, uno snapshot non comporta alcun sovraccarico delle prestazioni e non usa alcuno spazio. È possibile archiviare fino a 255 snapshot per volume nel tempo, tutti accessibili come versioni di sola lettura e online dei dati, consumando così poca capacità del numero di blocchi modificati tra ogni snapshot. I blocchi modificati vengono archiviati nel volume attivo. I blocchi a cui puntano gli snapshot vengono mantenuti (in sola lettura) nel volume per la custodia, da reimpiegare solo quando tutti gli snapshot (puntatori) sono stati cancellati. Pertanto, l'utilizzo del volume aumenterà nel tempo, mediante nuovi blocchi di dati o blocchi di dati (modificati) conservati negli snapshot.

 Il diagramma seguente Mostra gli snapshot di un volume e lo spazio usato nel tempo: 

![Diagramma che Mostra gli snapshot di un volume e lo spazio usato nel tempo](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Poiché uno snapshot del volume registra solo le modifiche del blocco dopo lo snapshot più recente, offre i vantaggi principali seguenti:

* Gli snapshot sono ***efficienza di archiviazione** _.   
    Gli snapshot utilizzano spazio di archiviazione minimo perché non copia i blocchi di dati dell'intero volume. Due snapshot presi in sequenza differiscono solo per i blocchi aggiunti o modificati nell'intervallo di tempo tra i due. Questo comportamento incrementale del blocco limita l'utilizzo della capacità di archiviazione associata. Molte implementazioni di snapshot alternativi utilizzano volumi di archiviazione uguali al file system attivo, aumentando i requisiti di capacità di archiviazione. A seconda delle percentuali di modifica a livello di _block giornaliero dell'applicazione, gli snapshot Azure NetApp Files utilizzeranno più o meno capacità, ma solo su dati modificati. Il consumo di snapshot giornalieri medio è compreso tra il 1-5% della capacità del volume utilizzato per molti volumi dell'applicazione o fino al 20-30% per i volumi, ad esempio SAP HANA volumi del database. Assicurarsi di [monitorare l'utilizzo di volumi e snapshot](azure-netapp-files-metrics.md#volumes) per il consumo di capacità snapshot rispetto al numero di snapshot creati e gestiti.   

* Gli snapshot sono ***rapidi per la creazione, la replica, il ripristino o la clonazione di** _.   
    La creazione, la replica, il ripristino o la clonazione di uno snapshot richiede solo pochi secondi, indipendentemente dalle dimensioni del volume e dal livello delle attività. È possibile creare uno snapshot [del volume su richiesta](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). È anche possibile usare i [criteri di snapshot](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) per specificare quando Azure NetApp files deve creare automaticamente uno snapshot e il numero di snapshot da tenere per un volume.  La coerenza delle applicazioni può essere eseguita tramite l'orchestrazione degli snapshot con il livello dell'applicazione, ad esempio usando lo [strumento AzAcSnap](azacsnap-introduction.md) per SAP Hana.

_ Gli snapshot non hanno alcun effetto sulle **prestazioni** del volume *.   
    Grazie alla natura di "Reindirizzamento in scrittura" della tecnologia di sottodisposizione, l'archiviazione o la conservazione di Azure NetApp Files snapshot non ha alcun effetto sulle prestazioni, anche con un'attività di dati intensa. Anche l'eliminazione di uno snapshot non ha un effetto minimo sulle prestazioni in molti casi. 

_ Gli snapshot forniscono ***scalabilità** _ perché possono essere creati spesso e molti possono essere conservati.   
    Azure NetApp Files volumi supportano fino a 255 snapshot. La possibilità di archiviare un numero elevato di snapshot di basso livello e di uso frequente aumenta la probabilità che la versione desiderata dei dati possa essere ripristinata correttamente.

_ Gli snapshot forniscono ***visibilità utente** _ e _*_ripristino file_*_.   
Le prestazioni elevate, la scalabilità e la stabilità di Azure NetApp Files tecnologia snapshot significa che fornisce un backup online ideale per il ripristino guidato dall'utente. Gli snapshot possono essere resi accessibili dagli utenti per scopi di ripristino di file, directory o volumi. Altre soluzioni consentono di copiare i backup nell'archiviazione offline o di [replicare tra aree](cross-region-replication-introduction.md) a scopo di conservazione o ripristino di emergenza.

## <a name="ways-to-create-snapshots"></a>Modalità di creazione di snapshot   

Gli snapshot Azure NetApp Files sono versatili in uso. Di conseguenza, sono disponibili più metodi per la creazione e la gestione di snapshot:

_ Manualmente (su richiesta), usando:   
    * Il [portale di Azure](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), l' [API REST](/rest/api/netapp/snapshots), l'interfaccia della riga di comando di [Azure](/cli/azure/netappfiles/snapshot)o gli strumenti [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)
    * Script (vedere [esempi](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Automatizzato tramite:
    * Criteri di snapshot, tramite l' [portale di Azure](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), l' [API REST](/rest/api/netapp/snapshotpolicies), l'interfaccia della riga di comando di [Azure](/cli/azure/netappfiles/snapshot/policy)o gli strumenti [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)
    * Strumenti di snapshot coerenti con l'applicazione, ad esempio [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Come i volumi e gli snapshot vengono replicati tra aree per il ripristino di emergenza  

Azure NetApp Files supporta la [replica tra aree](cross-region-replication-introduction.md) per scopi di ripristino di emergenza. Azure NetApp Files la replica tra aree utilizza la tecnologia SnapMirror. Solo i blocchi modificati vengono inviati in rete in un formato compresso ed efficiente. Dopo che una replica tra aree viene avviata tra i volumi, l'intero contenuto del volume (ovvero i blocchi di dati archiviati effettivi) viene trasferito una sola volta. Questa operazione è denominata *trasferimento di base*. Dopo il trasferimento iniziale, vengono trasferiti solo i blocchi modificati (acquisiti negli snapshot). Viene creata una replica 1:1 asincrona del volume di origine (inclusi tutti gli snapshot).  Questo comportamento segue un meccanismo di replica completo e incrementale per sempre. Questa tecnologia proprietaria riduce al minimo la quantità di dati che è necessario replicare tra le aree, consentendo così di risparmiare sui costi di trasferimento dei dati. Riduce inoltre il tempo di replica. È possibile ottenere un obiettivo del punto di ripristino (RPO) più piccolo, perché è possibile creare più snapshot e trasferirli più spesso con trasferimenti di dati limitati.

Il diagramma seguente mostra il traffico di snapshot negli scenari di replica tra aree: 

![Diagramma che mostra il traffico snapshot negli scenari di replica tra aree](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Modalità di ripristino dei dati da snapshot  

La tecnologia snapshot Azure NetApp Files migliora notevolmente la frequenza e l'affidabilità dei backup. Comporta un sovraccarico minimo delle prestazioni e può essere creato in modo sicuro su un volume attivo. Gli snapshot Azure NetApp Files consentono ripristini quasi istantanei, protetti e facoltativamente gestiti dall'utente. In questa sezione vengono descritti i vari modi in cui è possibile accedere o ripristinare i dati da Azure NetApp Files snapshot.

### <a name="restoring-files-or-directories-from-snapshots"></a>Ripristino di file o directory da snapshot 

Se la [visibilità del percorso dello snapshot](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) non è nascosta, gli utenti possono accedere direttamente agli snapshot per il recupero da eliminazioni accidentali, danneggiamenti o modifiche dei dati. La protezione dei file e delle directory viene mantenuta nello snapshot e gli snapshot sono di sola lettura in base alla progettazione. Di conseguenza, il ripristino è sicuro e semplice. 

Il diagramma seguente mostra l'accesso di file o directory a uno snapshot: 

![Diagramma che mostra l'accesso a un file o alla directory di uno snapshot](../media/azure-netapp-files/snapshot-file-directory-access.png)

Nel diagramma snapshot 1 utilizza solo i blocchi Delta tra il volume attivo e il momento della creazione dello snapshot. Tuttavia, quando si accede allo snapshot tramite il percorso dello snapshot del volume, i dati verranno *visualizzati* come se fosse la capacità completa del volume al momento della creazione dello snapshot. L'accesso alle cartelle snapshot consente agli utenti di eseguire il ripristino automatico dei dati copiando i file e le directory da uno snapshot scelto.

Analogamente, è possibile accedere in sola lettura agli snapshot nei volumi di replica tra aree di destinazione per il ripristino dei dati nell'area di ripristino di emergenza.  

Il diagramma seguente mostra l'accesso agli snapshot negli scenari di replica tra aree: 

![Diagramma che mostra l'accesso allo snapshot nella replica tra aree](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Vedere [ripristinare un file da uno snapshot usando un client](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) sul ripristino di singoli file o directory da snapshot.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Ripristino (clonazione) di uno snapshot in un nuovo volume

Azure NetApp Files snapshot possono essere ripristinati in un volume indipendente separato. Questa operazione è quasi immediata, indipendentemente dalle dimensioni del volume e dalla capacità utilizzata. Il volume appena creato è quasi immediatamente disponibile per l'accesso, mentre vengono copiati i blocchi di dati di volume e snapshot effettivi. A seconda delle dimensioni e della capacità del volume, questo processo può richiedere molto tempo durante il quale non è possibile eliminare il volume e lo snapshot padre. Tuttavia, è possibile accedere al volume dopo la creazione iniziale, mentre il processo di copia è in corso in background. Questa funzionalità consente la creazione rapida di volumi per il ripristino dei dati o la clonazione del volume per test e sviluppo. Per natura del processo di copia dei dati, l'utilizzo del pool di capacità di archiviazione raddoppierà al termine del ripristino e il nuovo volume visualizzerà la capacità attiva completa dello snapshot originale. Al termine di questo processo, il volume sarà indipendente e dissociato con il volume originale e i volumi e lo snapshot di origine potranno essere gestiti o rimossi in modo indipendente dal nuovo volume.

Il diagramma seguente mostra un nuovo volume creato ripristinando (clonando) uno snapshot:   

![Diagramma che mostra un nuovo volume creato tramite il ripristino di uno snapshot](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Le stesse operazioni possono essere eseguite su snapshot replicati in un volume di ripristino di emergenza. Qualsiasi snapshot può essere ripristinato in un nuovo volume, anche quando la replica tra aree rimane attiva o in corso. Questa funzionalità consente la creazione senza problemi di ambienti di test e sviluppo in un'area di ripristino di emergenza, inserendo i dati da usare, mentre i volumi replicati verrebbero altrimenti usati solo per scopi di ripristino di emergenza. Questo caso d'uso consente di isolare i test e lo sviluppo dalla produzione, eliminando il potenziale impatto sugli ambienti di produzione.  

Il diagramma seguente mostra il ripristino del volume (clonazione) con lo snapshot del volume di destinazione di ripristino di emergenza mentre è in corso la replica tra aree:  

![Diagramma che mostra il ripristino del volume con snapshot del volume di destinazione di ripristino di emergenza](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Vedere [ripristinare uno snapshot in un nuovo volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) informazioni sulle operazioni di ripristino del volume.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Ripristino di uno snapshot sul posto

In alcuni casi, poiché il nuovo volume utilizzerà la capacità di archiviazione, la creazione di un nuovo volume da uno snapshot potrebbe non essere necessaria o appropriata. Per eseguire rapidamente il ripristino dal danneggiamento dei dati (ad esempio, danneggiamenti del database o attacchi ransomware), potrebbe essere più appropriato ripristinare uno snapshot all'interno del volume stesso. Questa operazione può essere eseguita utilizzando la funzionalità di ripristino dello snapshot Azure NetApp Files. Questa funzionalità consente di ripristinare rapidamente un volume allo stato in cui si trovava quando è stato effettuato un particolare snapshot. Nella maggior parte dei casi, il ripristino di un volume è molto più rapido rispetto al ripristino di singoli file da uno snapshot al file system attivo, soprattutto in volumi di grandi dimensioni con più TiB. 

Il ripristino di uno snapshot del volume è quasi istantaneo e richiede solo pochi secondi per il completamento, anche per i volumi più grandi. I metadati del volume attivo (*tabella inode*) vengono sostituiti con i metadati dello snapshot al momento della creazione dello snapshot, eseguendo così il rollback del volume fino a quel momento specifico. Per rendere effettive le modifiche, non è necessario copiare alcun blocco di dati. Di conseguenza, è più efficiente dello spazio rispetto al ripristino di uno snapshot in un nuovo volume. 

Il diagramma seguente mostra un volume che ripristina uno snapshot precedente:  

![Diagramma che mostra un ripristino di un volume in uno snapshot precedente](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> I dati del file System attivo scritti e gli snapshot creati dopo la creazione dello snapshot selezionato andranno perduti. L'operazione di ripristino dello snapshot sostituirà tutti i dati nel volume di destinazione con i dati dello snapshot selezionato. Quando si seleziona uno snapshot, è necessario prestare attenzione al contenuto dello snapshot e alla data di creazione. Non è possibile annullare l'operazione di ripristino dello snapshot.  

Vedere [ripristinare un volume utilizzando snapshot per ripristinare](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) l'utilizzo di questa funzionalità.

## <a name="how-snapshots-are-deleted"></a>Modalità di eliminazione degli snapshot 

Gli snapshot utilizzano la capacità di archiviazione. Di conseguenza, non vengono in genere conservati per un periodo illimitato. Per la protezione dei dati, la conservazione e la recuperabilità, un numero di snapshot (creati in diversi momenti) viene in genere mantenuto online per una determinata durata, in base ai requisiti del contratto di RPO, RTO e conservazione. Tuttavia, gli snapshot meno recenti spesso non devono essere conservati nel servizio di archiviazione e potrebbe essere necessario eliminarli per liberare spazio. Qualsiasi snapshot può essere eliminato (non necessariamente in ordine di creazione) da un amministratore in qualsiasi momento. 

> [!IMPORTANT]
> L'operazione di eliminazione dello snapshot non può essere annullata. 

Quando si elimina uno snapshot, verranno rimossi tutti i puntatori da tale snapshot ai blocchi di dati esistenti. Quando un blocco di dati non ha più puntatori che puntano a esso (dal volume attivo o da altri snapshot del volume), il blocco di dati viene restituito allo spazio disponibile nel volume per un uso futuro. Pertanto, la rimozione di snapshot in genere consente di liberare più capacità in un volume rispetto all'eliminazione dei dati dal volume attivo, perché i blocchi di dati vengono spesso acquisiti negli snapshot creati in precedenza. 

Il diagramma seguente illustra l'effetto sul consumo di archiviazione dell'eliminazione di snapshot per un volume:  

![Diagramma che mostra l'effetto del consumo di archiviazione dell'eliminazione dello snapshot](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Assicurarsi di [monitorare l'utilizzo di volumi e snapshot](azure-netapp-files-metrics.md#volumes) e comprendere il modo in cui l'applicazione, il volume attivo e il consumo di snapshot interagiscono. 

Vedere [eliminare snapshot](azure-netapp-files-manage-snapshots.md#delete-snapshots) per la gestione dell'eliminazione di snapshot. Vedere [gestire i criteri di snapshot](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) su come automatizzare questo processo.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli snapshot tramite Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Monitorare le metriche di volumi e snapshot](azure-netapp-files-metrics.md#volumes)
* [Risolvere i problemi relativi ai criteri dello snapshot](troubleshoot-snapshot-policies.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video snapshot di Azure NetApp Files 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Snapshot NetApp-Catalogo video NetApp](https://tv.netapp.com/detail/video/2579133646001/snapshot)



