---
title: Collegamento diretto Azure Storage Explorer | Microsoft Docs
description: Documentazione del collegamento diretto Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582256"
---
# <a name="azure-storage-explorer-direct-link"></a>Collegamento diretto Azure Storage Explorer

In Windows e macOS Storage Explorer supporta gli URI con il `storageexplorer://` protocollo. Questi URI sono detti collegamenti diretti. Un collegamento diretto punta a una risorsa di archiviazione di Azure in Storage Explorer. In seguito a un collegamento diretto si apre Storage Explorer e si passa alla risorsa a cui punta. Questo articolo descrive il funzionamento di collegamenti diretti e il modo in cui è possibile usarli.

## <a name="how-direct-links-work"></a>Funzionamento di collegamenti diretti

Storage Explorer usa la visualizzazione albero per visualizzare le risorse in Azure. Un collegamento diretto contiene le informazioni gerarchiche per il nodo delle risorse collegate nell'albero. Quando viene seguito un collegamento diretto, Storage Explorer apre e riceve i parametri nel collegamento diretto. Storage Explorer quindi utilizza questi parametri per passare alla risorsa collegata nella visualizzazione albero.

> [!IMPORTANT]
> È necessario aver eseguito l'accesso e disporre delle autorizzazioni necessarie per accedere alla risorsa collegata affinché un collegamento diretto funzioni.

## <a name="parameters"></a>Parametri

Un collegamento diretto Storage Explorer inizia sempre con il protocollo `storageexplorer://` . Nella tabella seguente vengono illustrati i possibili parametri in un collegamento diretto.

Parametro | Descrizione
:---------| :---------
`v`         | Versione del protocollo di collegamento diretto.
`accountid` | ID risorsa Azure Resource Manager dell'account di archiviazione per la risorsa collegata. Se la risorsa collegata è un account di archiviazione, questo ID sarà il Azure Resource Manager ID risorsa dell'account di archiviazione. In caso contrario, questo ID sarà il Azure Resource Manager ID risorsa dell'account di archiviazione a cui appartiene la risorsa collegata.
`resourcetype` | facoltativo. Usato solo quando la risorsa collegata è un contenitore BLOB, una condivisione file, una coda o una tabella. Deve essere uno dei due "Azure. BlobContainer", "Azure. FileShare", "Azure. Queue", "Azure. FileShare".
`resourcename` | facoltativo. Usato solo quando la risorsa collegata è un contenitore BLOB, una condivisione file, una coda o una tabella. Nome della risorsa collegata.

Di seguito è riportato un esempio di collegamento diretto a un contenitore BLOB. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Ottenere un collegamento diretto da Storage Explorer

È possibile usare Storage Explorer per ottenere un collegamento diretto per una risorsa. Aprire il menu di scelta rapida del nodo per la risorsa nella visualizzazione albero. Usare quindi l'azione "copia collegamento diretto" per copiare il collegamento diretto negli Appunti.

## <a name="direct-link-limitations"></a>Limitazioni del collegamento diretto

I collegamenti diretti sono supportati solo per le risorse nei nodi di sottoscrizione. Sono inoltre supportati solo i tipi di risorse seguenti:

- Account di archiviazione
- Contenitori BLOB
- Code
- Condivisioni file
- Tabelle
