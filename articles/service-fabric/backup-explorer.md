---
title: Leggere e aggiornare un backup di raccolte Reliable Collections in locale
description: Usare Esplora backup in Azure Service Fabric per leggere e aggiornare un backup di raccolte Reliable Collections locale.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: a0131960d356e4862d1379c308e240e19e76205c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048069"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Leggere e aggiornare un backup di raccolte Reliable Collections tramite Esplora backup

Azure Service Fabric Backup Explorer consente la correzione dei dati in caso di danneggiamento dei dati in Service Fabric Reliable Collections. Lo stato corrente dei dati può essere danneggiato da qualsiasi bug introdotto in un'applicazione o da qualsiasi voce errata eseguita in un cluster attivo.

Con l'ausilio di Esplora backup, è possibile eseguire le attività seguenti:
-   Eseguire una query sui metadati per la raccolta.
-   Visualizzare lo stato corrente e le relative voci nella raccolta del backup caricato.
-   Elenca le transazioni eseguite dall'ultimo checkpoint.
-   Aggiornare la raccolta mediante l'aggiunta, l'aggiornamento o l'eliminazione di voci nella raccolta.
-   Eseguire un nuovo backup usando lo stato aggiornato.

> [!NOTE]
> Service Fabric Backup Explorer supporta attualmente solo la visualizzazione e la modifica di raccolte affidabili nel backup.
>

## <a name="access-the-backup"></a>Accedere al backup

Service Fabric Backup Explorer può essere utilizzato in uno dei modi seguenti per visualizzare o aggiornare le raccolte affidabili nel backup:
-   **Binario**: usare un pacchetto NuGet per visualizzare e modificare le raccolte Reliable Collections.
-   **Http/Rest**: usare un server REST basato su http per visualizzare e modificare Reliable Collections.
-   **bkpctl**: usare l'interfaccia della riga di comando Service Fabric Backup Explorer per visualizzare e modificare un backup Reliable Collections.

In Esplora backup è presente una libreria C# per utenti avanzati. È possibile usare direttamente la libreria nell'applicazione per lavorare con raccolte affidabili, in modo analogo al modo in cui i clienti lavorano con il gestore degli Stati nei servizi con stato esistenti. Per gli utenti di base e in un caso d'uso di base, Esplora risorse dispone anche di un server REST autonomo che espone le API per controllare i backup. Lo strumento dell'interfaccia della riga di comando di bkpctl funziona sulle API REST ed è basato su Python. È possibile utilizzare lo strumento CLI per leggere e aggiornare i backup e per eseguire nuovi backup tramite la riga di comando.

Per altre informazioni, vedere il repository GitHub [Service Fabric Backup Explorer](https://github.com/microsoft/service-fabric-backup-explorer) . Il repository contiene informazioni sull'origine e sulla versione e istruzioni di configurazione.

È anche possibile compilare il repository localmente e lavorare sui backup.
 
NuGet for Backup Explorer (Microsoft. ServiceFabric. ReliableCollectionBackup. Parser) sarà disponibile su [NuGet.org](https://www.nuget.org/). 

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulle [raccolte Reliable Collections in Azure Service Fabric i servizi con stato](service-fabric-reliable-services-reliable-collections.md).
* Esaminare [Service Fabric procedure consigliate](./service-fabric-best-practices-security.md).