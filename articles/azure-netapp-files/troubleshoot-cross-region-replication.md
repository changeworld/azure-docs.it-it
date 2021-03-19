---
title: Risolvere i problemi di replica tra aree diverse per Azure NetApp Files | Microsoft Docs
description: Vengono descritti i messaggi di errore e le risoluzioni che consentono di risolvere i problemi di replica tra aree per Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: d3d944646689e9e6189b0343e8bf67c8fb0abcbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590926"
---
# <a name="troubleshoot-cross-region-replication"></a>Risolvere i problemi relativi alla replica tra più aree

Questo articolo descrive i messaggi di errore e le risoluzioni che consentono di risolvere i problemi di replica tra aree per Azure NetApp Files. 

## <a name="errors-creating-replication"></a>Errori durante la creazione della replica  

|     Messaggio di errore    |     Soluzione    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     Non è possibile creare una replica con un volume di origine già presente in una relazione di replica dei dati.    |
|     `Peered region   '{0}' is not accepted`    |     Si sta tentando di creare una replica tra le aree senza peering.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     Verificare che l'ID risorsa remota sia un ID di risorsa volume.    |

## <a name="errors-authorizing-volume"></a>Errori di autorizzazione del volume  

|     Messaggio di errore    |     Soluzione    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     `RemoteResourceID`Manca o non è valido dall'interfaccia utente o dalla richiesta API (correzione del messaggio di errore).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     L'oggetto   `RemoteResourceID` è mancante o non valido dall'interfaccia utente o dalla richiesta dell'API.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     Verificare se   `RemoteResourceID` è corretto o esiste per l'utente.    |
|     `Remote volume   '{0}' is not configured for replication`    |     Il volume di destinazione non è un volume di protezione dati.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     Il volume di protezione dei dati non contiene questo volume di origine nell'ID risorsa remota. è stato immesso un ID origine errato.    |
|     `The   destination volume replication creation failed (message: {0})`    |     Questo errore indica un errore del server. Contatto supporto.    |

## <a name="errors-deleting-replication"></a>Errori durante l'eliminazione della replica

|     Messaggio di errore    |     Soluzione    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     Verificare che la replica sia stata interruppe oppure che sia non inizializzata e inattiva (inizializzazione non riuscita).    |
|     `Cannot delete   source replication`    |     L'eliminazione della replica dal lato di origine non è consentita. Assicurarsi di eliminare la replica dal lato di destinazione.    |

## <a name="errors-deleting-volume"></a>Errori durante l'eliminazione del volume

|     Messaggio di errore    |     Soluzione    |
|-|-|
| `Volume is a member of an active volume replication relationship`  |  Eliminare la replica prima di eliminare il volume. Vedere [eliminare le repliche](cross-region-replication-delete.md). Per questa operazione è necessario interrompere il peering prima di eliminare la replica per il volume. |
| `Volume with replication cannot be deleted`  |  Eliminare la replica prima di eliminare il volume. Vedere [eliminare le repliche](cross-region-replication-delete.md). Per questa operazione è necessario interrompere il peering prima di eliminare la replica per il volume. 

## <a name="errors-resyncing-volume"></a>Errori di risincronizzazione del volume

|     Messaggio di errore    |     Soluzione    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Verificare che la replica del volume sia in stato "interruppe".    |

## <a name="errors-deleting-snapshot"></a>Errori durante l'eliminazione dello snapshot 

|     Messaggio di errore    |     Soluzione    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Verificare che la replica del volume sia stata interruppe se si desidera eliminare questo snapshot.    |
|     `Cannot delete   volume replication generated snapshot`    |     L'eliminazione degli snapshot della linea di base della replica non è consentita.    |

## <a name="errors-resizing-volumes"></a>Errori di ridimensionamento di volumi

|     Messaggio di errore    |     Soluzione    |
|-|-|
|   Il tentativo di ridimensionare un volume di origine non è riuscito con l'errore `"PoolSizeTooSmall","message":"Pool size too small for total volume size."`  |  Assicurarsi di disporre di spazio sufficiente nei pool di capacità per i volumi di origine e di destinazione della replica tra aree. Quando si ridimensiona il volume di origine, il volume di destinazione viene ridimensionato automaticamente. Tuttavia, se il pool di capacità che ospita il volume di destinazione non dispone di spazio sufficiente, il ridimensionamento dei volumi di origine e di destinazione avrà esito negativo. Per informazioni dettagliate, vedere [ridimensionare un volume di destinazione della replica tra aree](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume) .   |

## <a name="next-steps"></a>Passaggi successivi  

* [Replica tra più aree](cross-region-replication-introduction.md)
* [Requisiti e considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md)
* [Creare la replica di un volume](cross-region-replication-create-peering.md)
* [Visualizzare lo stato integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Gestire il ripristino di emergenza](cross-region-replication-manage-disaster-recovery.md)
* [Ridimensionare un volume di destinazione della replica tra aree](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Risolvere i problemi relativi alla replica tra più aree](troubleshoot-cross-region-replication.md)
