---
title: Eseguire il mapping di una struttura di cartelle a una topologia di Sincronizzazione file di Azure
description: Eseguire il mapping di una struttura di file e cartelle esistente alle condivisioni file di Azure per l'uso con Sincronizzazione file di Azure. Un blocco di testo comune, condiviso tra documenti di migrazione.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547552"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Opzione              | Significato |
|---------------------|---------|
| /MT                 | Consente a RoboCopy di eseguire multithread. Il valore predefinito è 8 e il valore massimo è 128. Corrisponde a questo valore al numero di core del processore e al numero di thread per core. Considerare se i core devono essere riservati ad altre attività che possono essere presenti in un server di produzione. |
| /NP                 | Non verrà visualizzato lo stato di avanzamento della copia per ogni file e cartella. La visualizzazione dello stato di avanzamento riduce significativamente le prestazioni di copia. |
| /NFL                | Specifica che i nomi dei file non vengono registrati. Migliora le prestazioni di copia. |
| /NDL                | Specifica che i nomi delle directory non vengono registrati. Migliora le prestazioni di copia. |
| / B                  | Esegue RoboCopy nella stessa modalità usata da un'applicazione di backup. Consente a RoboCopy di spostare i file che l'utente corrente non dispone delle autorizzazioni per. |
| /MIR                | L' *origine mirror per la destinazione* consente a Robocopy di copiare solo i delta tra l'origine e la destinazione. Verranno copiate le sottodirectory vuote. Gli elementi (file o cartelle) che sono stati modificati o non esistono nella destinazione verranno copiati. Gli elementi presenti nella destinazione ma non nell'origine verranno eliminati (eliminati) dalla destinazione. Quando si usa questa opzione, è necessario che la struttura di cartelle di origine e di destinazione corrisponda esattamente. "Corrispondenza" significa che si esegue la copia dall'origine e dal livello di cartella corretti al livello di cartella corrispondente nella destinazione. Solo a questo punto è possibile che la copia "catch up" abbia esito positivo. Quando l'origine e la destinazione non corrispondono, l'utilizzo `/MIR` di consentirà l'eliminazione e la Ricopia di grandi dimensioni. |
| /IT                 | Garantisce che la fedeltà venga mantenuta in determinati scenari di mirroring. </br>*Esempio* : se tra due operazioni di Robocopy viene eseguito un file si verifica una modifica dell'ACL e un aggiornamento degli attributi, ad esempio, è contrassegnato come *nascosto*. Senza/IT, la modifica dell'ACL può essere persa da RoboCopy e non trasferiti nel percorso di destinazione. |
|Copia`[copyflags]`  | Fedeltà della copia del file (impostazione predefinita se non è specificato è `/COPY:DAT` ), copia flag: `D` = data, `A` = attributi, `T` = timestamp, `S` = sicurezza = ACL NTFS, `O` = informazioni sul proprietario, `U` = informazioni sul tipo<u>u</u>. Le informazioni di controllo non possono essere archiviate in una condivisione file di Azure. |
| /DCOPY:`[copyflags]`| Fedeltà per la copia delle directory (il valore predefinito se non è specificato è `/DCOPY:DA` ), Copy Flags: `D` = data, `A` = Attributes, `T` = timestamps. |
| /UNILOG:<file name> | Restituisce lo stato del file di LOG come UNICODE (sovrascrive il log esistente). |
| /LFSM               | **solo per le destinazioni con archiviazione a livelli** </br>L'uso di/LFSM richiede a RoboCopy di operare in modalità di spazio libero insufficiente. Questa opzione è utile solo per le destinazioni con archiviazione a livelli, che potrebbe esaurire la capacità locale prima che RoboCopy possa terminare. Questa opzione è stata aggiunta in modo specifico per l'uso con una destinazione Sincronizzazione file di Azure la suddivisione in livelli cloud abilitata. Può essere utilizzato indipendentemente dalla Sincronizzazione file di Azure. In questa modalità, RoboCopy verrà sospeso ogni volta che la copia di un file causerebbe lo spazio libero del volume di destinazione al di sotto di un valore "Floor". Questo valore può essere specificato dal `/LFSM:n` formato del flag. Il parametro `n` viene specificato in base 2: `nKB` , `nMB` o `nGB` . Se `/LFSM` viene specificato senza valore di piano esplicito, il piano viene impostato sul 10% delle dimensioni del volume di destinazione. La modalità di spazio disponibile insufficiente è incompatibile con/MT,/EFSRAW,/B e/ZB. |
| /Z                  | **uso attento** </br>Copia i file in modalità di riavvio. è consigliabile usare solo in un ambiente di rete instabile. Questa opzione riduce in modo significativo le prestazioni di copia grazie alla registrazione aggiuntiva. |
| /ZB                 | **uso attento** </br>Usa la modalità di riavvio. Se l'accesso viene negato, questa opzione Usa la modalità di backup. Questa opzione riduce in modo significativo le prestazioni di copia a causa del checkpoint. |
   