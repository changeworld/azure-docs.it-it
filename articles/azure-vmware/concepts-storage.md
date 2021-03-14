---
title: Concetti-archiviazione
description: Informazioni sulle funzionalità di archiviazione chiavi in cloud privati della soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: 2551b11ada8b6118fb7524d9fe36f8b4b568aca1
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462474"
---
#  <a name="azure-vmware-solution-storage-concepts"></a>Concetti relativi all'archiviazione della soluzione VMware di Azure

I cloud privati della soluzione VMware di Azure forniscono l'archiviazione nativa a livello di cluster con VMware rete VSAN. Tutte le archiviazioni locali da ogni host in un cluster vengono usate in un archivio dati rete VSAN e la crittografia dei dati inattivi è disponibile e abilitata per impostazione predefinita. È possibile usare le risorse di archiviazione di Azure per estendere le funzionalità di archiviazione dei cloud privati.

## <a name="vsan-clusters"></a>cluster rete VSAN

L'archiviazione locale in ogni host del cluster viene usata come parte di un archivio dati rete VSAN. Tutti diskgroups usano un livello di cache NVMe di 1,6 TB con la capacità basata su SSD, per host, di 15,4 TB. La dimensione del livello di capacità raw di un cluster è la capacità per host per il numero di host. Un cluster host, ad esempio, offre una capacità di 61,6 TB non elaborata nel livello di capacità rete VSAN.

L'archiviazione locale negli host del cluster viene usata nell'archivio dati rete VSAN a livello di cluster. Tutti gli archivi dati vengono creati come parte di una distribuzione di cloud privato e sono disponibili per l'uso immediato. L'utente cloudadmin e tutti gli utenti nel gruppo CloudAdmin possono gestire gli archivi dati con questi privilegi rete VSAN:

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Archivio dati. DeleteFile
- Archivio dati. filemanagement
- Archivio dati. UpdateVirtualMachineMetadata

## <a name="data-at-rest-encryption"></a>Crittografia dei dati inattivi

per impostazione predefinita, i datastore rete VSAN usano la crittografia dei dati inattivi. La soluzione di crittografia è basata sul KMS e supporta le operazioni vCenter per la gestione delle chiavi. Le chiavi vengono archiviate crittografate da una chiave master Azure Key Vault. Quando un host viene rimosso da un cluster, i dati sulle unità SSD vengono invalidati immediatamente.

## <a name="scaling"></a>Ridimensionamento

La capacità di archiviazione del cluster nativa viene ridimensionata aggiungendo host a un cluster. Per i cluster che usano host AVS36, la capacità a livello di cluster non elaborata viene aumentata di 15,4 TB con ogni host aggiunto. Gli host possono richiedere circa 10 minuti per essere aggiunti a un cluster.  Per istruzioni sulla scalabilità dei cluster, vedere l' [esercitazione scalare un cloud privato][tutorial-scale-private-cloud].

## <a name="azure-storage-integration"></a>Integrazione di archiviazione di Azure

È possibile usare i servizi di archiviazione di Azure nei carichi di lavoro in esecuzione nel cloud privato. I servizi di archiviazione di Azure includono gli account di archiviazione, l'archiviazione tabelle e l'archiviazione BLOB. La connessione dei carichi di lavoro ai servizi di archiviazione di Azure non attraversa Internet. Questa connettività offre maggiore sicurezza e consente di usare i servizi di archiviazione di Azure basati sul contratto di servizio nei carichi di lavoro del cloud privato.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati illustrati i concetti relativi all'archiviazione della soluzione VMware di Azure, è possibile acquisire familiarità con:

- [Concetti di identità del cloud privato](concepts-identity.md).
- [controllo degli accessi in base al ruolo vSphere per la soluzione VMware di Azure](concepts-role-based-access-control.md).
- [Come abilitare la risorsa della soluzione VMware di Azure](enable-azure-vmware-solution.md).
- [Azure NetApp Files con la soluzione VMware di Azure](netapp-files-with-azure-vmware-solution.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
