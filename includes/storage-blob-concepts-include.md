---
title: includere file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612945"
---
L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archiviazione BLOB è ottimizzata per l'archiviazione di grandi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari.

## <a name="about-blob-storage"></a>Informazioni sull'archiviazione BLOB

L'archiviazione BLOB è progettata per:

* Invio di immagini o documenti direttamente in un browser.
* Archiviazione di file per l'accesso distribuito.
* Streaming di audio e video.
* Scrittura in file di log.
* Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
* Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

Gli utenti o le applicazioni client possono accedere agli oggetti nell'archiviazione BLOB tramite HTTP/HTTPS ovunque si trovino. Gli oggetti nell'archiviazione BLOB sono accessibili tramite l'[API REST di Archiviazione di Azure](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/az.storage), l'[interfaccia della riga di comando di Azure](/cli/azure/storage) o una libreria client di Archiviazione di Azure. Le librerie client sono disponibili per diversi linguaggi, tra cui:

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Informazioni su Azure Data Lake Storage Gen2

L'archiviazione BLOB supporta Azure Data Lake Storage Gen2, la soluzione aziendale di analisi di Big Data Microsoft per il cloud. Azure Data Lake Storage Gen2 offre un file system gerarchico e i vantaggi dell'archiviazione BLOB, tra cui:

* Archiviazione a più livelli e a basso costo
* Disponibilità elevata
* Coerenza assoluta
* Funzionalità di ripristino di emergenza

Per altre informazioni su Data Lake Storage Gen2, vedere [Introduzione ad Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md).