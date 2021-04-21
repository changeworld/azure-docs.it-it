---
title: Sincronizzazione file di Azure di livelli cloud | Microsoft Docs
description: Informazioni dettagliate sul funzionamento dei criteri relativi alla data e allo spazio disponibile nel volume per scenari diversi.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797040"
---
# <a name="cloud-tiering-policies"></a>Criteri di livelli cloud

La creazione di livelli cloud include due criteri che determinano quali file sono a livelli nel cloud: i criteri di spazio libero del **volume** e **i criteri di data.**

I **criteri di spazio libero del** volume assicurano che una percentuale specificata del volume locale in cui si trova l'endpoint server sia sempre libera. 

Data **dell'ultimo accesso** ai file dei livelli dei criteri x giorni fa o versioni successive. I criteri di spazio libero del volume avranno sempre la precedenza. Quando lo spazio disponibile nel volume non è sufficiente per archiviare il numero di giorni di file descritto dai criteri di data, Sincronizzazione file di Azure eseguirà l'override dei criteri di data e continuerà a livelli i file più a freddo fino a quando non viene soddisfatta la percentuale di spazio disponibile nel volume.

## <a name="how-both-policies-work-together"></a>Funzionamento di entrambi i criteri

Si userà un esempio per illustrare il funzionamento di questi criteri: si supponga di aver configurato Sincronizzazione file di Azure in un volume locale di 500 GB e che la livellistica cloud non sia mai stata abilitata. Questi sono i file nella condivisione file:

|File Name |Ora ultimo accesso  |Dimensioni file  |Archiviato in |
|----------|------------------|-----------|----------|
|File 1    | 2 giorni fa  | 10 GB | Server e condivisione file di Azure
|File 2    | 10 giorni fa | 30 GB | Server e condivisione file di Azure
|File 3    | 1 anno fa | 200 GB | Server e condivisione file di Azure
|File 4    | 1 anno e 2 giorni fa | 130 GB | Server e condivisione file di Azure
|File 5    | 2 anni, 1 giorno fa | 140 GB | Server e condivisione file di Azure

**Modifica 1:** È stata abilitata la funzionalità di cloud a livelli, è stato impostato un criterio di spazio disponibile del volume del 20% e i criteri di data sono stati mantenuti disabilitati. Con questa configurazione, il cloud a livelli garantisce il 20% (in questo caso 100 GB) di spazio disponibile nel computer locale. Di conseguenza, la capacità totale della cache locale è 400 GB. Questi 400 GB archivieranno i file usati più di recente e di frequente nel volume locale.

Con questa configurazione, solo i file da 1 a 4 verrebbero archiviati nella cache locale e il file 5 verrebbe archiviato a livelli. Si tratta solo di 370 GB su 400 GB che possono essere usati. Il file 5 è di 140 GB e supererebbe il limite di 400 GB se fosse memorizzato nella cache locale. 

**Modifica 2:** Si dice che un utente accede al file 5. In questo modo il file 5 è il file a cui si accede più di recente nella condivisione. Di conseguenza, il file 5 verrebbe archiviato nella cache locale e, per rientrare nel limite di 400 GB, il file 4 verrebbe archiviato a livelli. La tabella seguente illustra dove sono archiviati i file, con questi aggiornamenti:

|File Name |Ora ultimo accesso  |Dimensioni file  |Archiviato in |
|----------|------------------|-----------|----------|
|File 5    | 2 ore fa | 140 GB | Server e condivisione file di Azure
|File 1    | 2 giorni fa  | 10 GB | Server e condivisione file di Azure
|File 2    | 10 giorni fa | 30 GB | Server e condivisione file di Azure
|File 3    | 1 anno fa | 200 GB | Server e condivisione file di Azure
|File 4    | 1 anno e 2 giorni fa | 130 GB | Condivisione file di Azure a livelli in locale

**Modifica 3:** Si noti che i criteri sono stati aggiornati in modo che i criteri di a livelli basati su data sia di 60 giorni e che i criteri di spazio disponibile nel volume sia 70%. A questo punto, nella cache locale è possibile archiviare solo fino a 150 GB. Anche se l'accesso al file 2 è stato eseguito meno di 60 giorni fa, i criteri di spazio libero del volume eseguiranno l'override dei criteri di data e il file 2 viene a livelli per mantenere il 70% di spazio disponibile locale.

**Modifica 4:** Se i criteri di spazio disponibile nel volume sono stati modificati al 20% e quindi usati per richiamare tutti i file che si adattano all'unità locale rispettando i criteri di livelli cloud, la tabella sarà simile alla `Invoke-StorageSyncFileRecall` seguente:

|File Name |Ora ultimo accesso  |Dimensioni file  |Archiviato in |
|----------|------------------|-----------|----------|
|File 5    | 1 ora fa  | 140 GB | Server e condivisione file di Azure
|File 1    | 2 giorni fa  | 10 GB | Server e condivisione file di Azure
|File 2    | 10 giorni fa | 30 GB | Server e condivisione file di Azure
|File 3    | 1 anno fa | 200 GB | Condivisione file di Azure, a livelli in locale
|File 4    | 1 anno, 2 giorni fa | 130 GB | Condivisione file di Azure, a livelli in locale

In questo caso, i file 1, 2 e 5 vengono memorizzati nella cache locale e i file 3 e 4 vengono archiviati a livelli. Poiché i criteri di data sono di 60 giorni, i file 3 e 4 sono a livelli, anche se i criteri di spazio disponibile nel volume consentono fino a 400 GB in locale.

> [!NOTE] 
> I file non vengono richiamati automaticamente quando i clienti modificano i criteri di spazio disponibile nel volume su un valore inferiore (ad esempio, dal 20% al 10%) oppure modificare i criteri di data in un valore maggiore, ad esempio da 20 a 50 giorni.

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Più endpoint server in un volume locale

Il cloud a livelli può essere abilitato per più endpoint server in un singolo volume locale. Per questa configurazione, è necessario impostare lo spazio disponibile nel volume sulla stessa quantità per tutti gli endpoint server nello stesso volume. Se si impostano criteri di spazio disponibile nel volume diversi per diversi endpoint server nello stesso volume, la percentuale di spazio disponibile nel volume più grande avrà la precedenza. Questo criterio è denominato criterio **di spazio disponibile nel volume effettivo.** Ad esempio, se si dispone di tre endpoint server nello stesso volume locale, uno impostato su 15%, un altro impostato su 20% e un terzo impostato su 30%, inizieranno tutti a livelli i file meno recenti quando hanno meno del 30% di spazio disponibile.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare il cloud a livelli](file-sync-monitor-cloud-tiering.md)
