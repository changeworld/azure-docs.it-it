---
title: Account Batch e account di Archiviazione di Azure
description: Informazioni sugli accunt Azure Batch e su come vengono usati dal punto di vista dello sviluppo.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 83108a265f91c9feef2fab424f1819939c2d58c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896749"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Account Batch e account di Archiviazione di Azure

Un account Azure Batch è un'entità identificata in modo univoco all'interno del servizio Batch. La maggior parte delle soluzioni Batch usa di [Archiviazione di Azure](../storage/index.yml) per archiviare file di risorse e file di output, quindi ogni account Batch è in genere associato a un account di archiviazione corrispondente.

## <a name="batch-accounts"></a>Account Batch

Tutte le operazioni di elaborazione e le risorse sono associata a un account Batch. Quando l'applicazione effettua una richiesta sul servizio Batch, autentica la richiesta usando il nome dell'account Azure Batch, l'URL dell'account e una chiave di accesso o un token di Azure Active Directory.

È possibile eseguire più carichi di lavoro Batch in un singolo account Batch. È anche possibile distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

È possibile creare un account Batch usando il [portale di Azure](batch-account-create-portal.md) o a livello di codice, ad esempio con la [libreria di gestione .NET per Batch](batch-management-dotnet.md). Quando si crea l'account, è possibile associare un account di archiviazione di Azure per l'archiviazione di applicazioni e dati di input e output correlati al processo.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]


## <a name="azure-storage-accounts"></a>Account di archiviazione di Azure

La maggior parte delle soluzioni Batch usa l'Archiviazione di Azure per archiviare i file delle risorse e i file di output. Ad esempio, le attività di Batch, incluse le attività standard, le attività di avvio, le attività di preparazione del processo e le attività di rilascio del processo, devono specificare in genere file di risorse che si trovano in un account di archiviazione. Gli account di archiviazione archiviano anche i dati elaborati e i dati di output generati.

Batch supporta i tipi di account di Archiviazione di Azure seguenti:

- Account per utilizzo generico v2
- Account per utilizzo generico v1
- Gli account di archiviazione BLOB (attualmente supportati per i pool nella configurazione macchina virtuale)

Per altre informazioni sugli account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](../storage/common/storage-account-overview.md).

È possibile associare un account di archiviazione all'account Batch quando si crea un account Batch o in seguito. Prendere in considerazione i requisiti relativi a costi e prestazioni durante la scelta di un account di archiviazione. Ad esempio, le opzioni dell'account di archiviazione per utilizzo generico v2 e BLOB supportano [limiti di capacità e scalabilità](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) superiori rispetto all'utilizzo generico v1. Contattare il supporto tecnico di Azure per richiedere un incremento del limite di archiviazione. Queste opzioni dell'account possono migliorare le prestazioni delle soluzioni Batch che contengono un numero elevato di attività parallele che leggono da o scrivono nell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [nodi e pool](nodes-and-pools.md).
- Informazioni su come creare e gestire account batch usando il [portale di Azure](batch-account-create-portal.md) o [batch Management .NET](batch-management-dotnet.md).
- Informazioni su come usare gli [endpoint privati](private-connectivity.md) con account Azure batch.
