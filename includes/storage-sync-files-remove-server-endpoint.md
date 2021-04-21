---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2f50a1d046b7b7d3fd7507ef87f8f29096a4faa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774567"
---
No: la rimozione di un endpoint server non è come il riavvio di un server. La rimozione e la ricreazione dell'endpoint server non è quasi mai una soluzione appropriata per risolvere i problemi di sincronizzazione, a livelli cloud o ad altri aspetti della Sincronizzazione file di Azure. La rimozione di un endpoint server è un'operazione distruttiva. Può causare la perdita di dati nel caso in cui i file a livelli esistano all'esterno dello spazio dei nomi dell'endpoint server. Per altre informazioni, vedere Perché [esistono file a livelli all'esterno dello](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) spazio dei nomi dell'endpoint server. Oppure può causare file inaccessibili per i file a livelli presenti all'interno dello spazio dei nomi dell'endpoint server. Questi problemi non vengono risolti quando viene ricreato l'endpoint server. I file a livelli potrebbero esistere all'interno dello spazio dei nomi dell'endpoint server anche se la suddivisione a livelli del cloud non è mai stata abilitata. Per questo motivo è consigliabile non rimuovere l'endpoint server a meno che non si desideri interrompere l'uso di Sincronizzazione file di Azure con questa cartella specifica o non sia stato richiesto in modo esplicito da un tecnico Microsoft. Per altre informazioni sulla rimozione degli endpoint server, vedere [Rimuovere un endpoint server](../articles/storage/file-sync/file-sync-server-endpoint.md#remove-a-server-endpoint).    
