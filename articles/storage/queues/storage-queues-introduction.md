---
title: Introduzione ad Archiviazione code di Azure - Archiviazione di Azure
description: Introduzione ad Archiviazione code di Azure, un servizio per l'archiviazione di un numero elevato di messaggi. Un servizio di archiviazione code contiene un formato di URL, un account di archiviazione, una coda e un messaggio.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8c5c97fbb72934dd99ec784ccf8e08eec059c31b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590580"
---
# <a name="what-is-azure-queue-storage"></a>Che cos'è Archiviazione code di Azure?

Archiviazione code di Azure è un servizio per l'archiviazione di un numero elevato di messaggi. È possibile accedere ai messaggi ovunque ci si trovi con chiamate autenticate tramite HTTP o HTTPS. Un messaggio in coda avere dimensioni fino a 64 KB. Una coda può contenere milioni di messaggi, fino al limite di capacità totale dell'account di archiviazione. Le code vengono in genere usate per creare un backlog di lavoro da elaborare in modo asincrono.

## <a name="queue-storage-concepts"></a>Concetti relativi ad Archiviazione code

Archiviazione code contiene i componenti seguenti:

![Diagramma che mostra la relazione tra un account di archiviazione, le code e i messaggi.](./media/storage-queues-introduction/queue1.png)

- **Formato dell'URL**: è possibile fare riferimento alle code usando il formato di URL seguente:

  `https://<storage account>.queue.core.windows.net/<queue>`

  L'URL seguente fa riferimento a una delle code nel diagramma: 

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per informazioni sulla capacità degli account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Coda:** una coda contiene un set di messaggi. Il nome della coda **deve** essere tutto in minuscolo. Per informazioni sull'assegnazione di nomi alle code, vedere [Denominazione di code e metadati](/rest/api/storageservices/naming-queues-and-metadata).

- **Messaggio:** un messaggio, in qualsiasi formato, con dimensioni massime di 64 KB. Prima della versione 2017-07-29, la durata (TTL) massima consentita è di sette giorni. Per la versione 2017-07-29 o successive, la durata massima consentita può essere un numero positivo qualsiasi o -1, a indicare che il messaggio non scade. Se questo parametro viene omesso, la durata predefinita è di sette giorni.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazione](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Introduzione all'archiviazione code con .NET](storage-dotnet-how-to-use-queues.md)
- [Introduzione all'archiviazione code con Java](storage-java-how-to-use-queue-storage.md)
- [Introduzione all'archiviazione code con Python](storage-python-how-to-use-queue-storage.md)
- [Introduzione all'archiviazione code con Node.js](storage-nodejs-how-to-use-queues.md)
