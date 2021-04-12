---
title: Risoluzione dei problemi di importazione ed esportazione in importazione/esportazione di Azure | Microsoft Docs
description: Informazioni su come gestire problemi comuni quando si usa l'importazione/esportazione di Azure.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706429"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Risolvere i problemi relativi all'importazione/esportazione di Azure
Questo articolo descrive come risolvere i problemi comuni durante l'importazione e l'esportazione di dati in importazione/esportazione di Azure.

## <a name="a-copy-session-failed-what-i-should-do"></a>Una sessione di copia non è riuscita. Cosa devo fare?  

Quando una sessione di copia ha esito negativo, sono disponibili due opzioni:  
* Se è possibile ritentare l'errore, ad esempio se la condivisione di rete era offline per un breve periodo di tempo e ora è di nuovo online, è possibile riprendere la sessione di copia.
* Se non è possibile ritentare l'errore, ad esempio se è stata specificata la directory dei file di origine errata nei parametri della riga di comando, è necessario interrompere la sessione di copia.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Non è possibile riprendere o interrompere una sessione di copia.

Se la sessione di copia è la prima sessione di copia per un'unità, dovrebbe essere visualizzato un messaggio di errore che indica che "la prima sessione di copia non può essere ripresa o interrotta". In questo caso, è possibile eliminare il file journal precedente e rieseguire il comando.  

Se una sessione di copia non è la prima per un'unità, la sessione può sempre essere ripresa o interrotta.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Il file journal è stato perso. Posso ancora creare il processo?

Il file journal per un'unità contiene le informazioni complete sulla sessione della copia dei dati. Quando si aggiungono file all'unità, il file journal viene usato per creare un processo di importazione. Se si perde il file journal, sarà necessario ripetere tutte le sessioni di copia per l'unità.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare lo strumento di importazione/esportazione di Azure](storage-import-export-tool-setup-v1.md)
* [Preparare i dischi rigidi per un processo di importazione](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Esaminare lo stato del processo con i file di log di copia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Ripristinare un processo di importazione](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Ripristinare un processo di esportazione](storage-import-export-tool-repairing-an-export-job-v1.md)