---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582109"
---
Le avvertenze seguenti si applicano ai dati spostati in Azure.

- È consigliabile evitare che più dispositivi scrivano nello stesso contenitore.
- Se si dispone di un oggetto di Azure esistente, ad esempio un BLOB o un file, nel cloud con lo stesso nome dell'oggetto da copiare, il dispositivo sovrascriverà il file nel cloud.
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle di condivisione non viene caricata nei contenitori BLOB.
- È possibile copiare i dati tramite il trascinamento della selezione con Esplora file o tramite la riga di comando. Se le dimensioni aggregate dei file copiati sono maggiori di 10 GB, è consigliabile utilizzare un programma di copia bulk, ad esempio `Robocopy` o `rsync` . Gli strumenti per la copia bulk ritentano l'operazione di copia per errori intermittenti e forniscono resilienza aggiuntiva.
- Se la condivisione associata al contenitore di archiviazione di Azure consente di caricare BLOB che non corrispondono al tipo di BLOB definito per la condivisione al momento della creazione, questi BLOB non vengono aggiornati. Ad esempio, se si crea una condivisione BLOB in blocchi sul dispositivo, associare la condivisione a un contenitore cloud esistente con BLOB di pagine, aggiornare tale condivisione per scaricare i file e quindi modificare alcuni dei file aggiornati già archiviati come BLOB di pagine nel cloud. verranno visualizzati errori di caricamento.
- Dopo aver creato un file nelle condivisioni, non è possibile rinominarlo.
- Se si elimina un file da una condivisione, la voce corrispondente nell'account di archiviazione non viene eliminata.
- Se si usa `rsync` per copiare i dati, l'opzione `rsync -a` non è supportata.