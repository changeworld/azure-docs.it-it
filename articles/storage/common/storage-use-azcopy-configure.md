---
title: Trovare gli errori & i processi con i log in AzCopy (Archiviazione di Azure) | Microsoft Docs
description: Informazioni su come usare i log per diagnosticare gli errori e riprendere i processi sospesi usando i file di piano.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502896"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Trovare gli errori e riprendere i processi usando i file di log e di piano in AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo illustra come usare i log per diagnosticare gli errori e quindi usare i file di piano per riprendere i processi. Questo articolo illustra anche come configurare i file di log e di piano modificandone il livello di dettaglio e il percorso predefinito in cui sono archiviati.

> [!NOTE]
> Per informazioni su come iniziare a usare AzCopy, vedere Introduzione ad [AzCopy.](storage-use-azcopy-v10.md)

## <a name="log-and-plan-files"></a>File di log e di piano

AzCopy crea *file di log* e *di* piano per ogni processo. È possibile usare questi log per analizzare e risolvere eventuali problemi potenziali. 

I log conterranno lo stato dell'errore ( , e ), il `UPLOADFAILED` percorso completo e il motivo `COPYFAILED` `DOWNLOADFAILED` dell'errore.

Per impostazione predefinita, i file di log e di piano si trovano nella directory in Windows o nella directory in Mac e Linux, ma è `%USERPROFILE%\.azcopy` `$HOME$\.azcopy` possibile modificare tale percorso. 

L'errore pertinente non è necessariamente il primo errore visualizzato nel file. Per errori quali errori di rete, timeout ed errori server occupato, AzCopy ritenterà fino a 20 volte e in genere il processo di ripetizione dei tentativi avrà esito positivo.  Il primo errore visualizzato potrebbe essere un elemento innocuo che è stato ritentato correttamente.  Quindi, anziché esaminare il primo errore nel file, cercare gli errori che si avvicinano `UPLOADFAILED` a `COPYFAILED` , o `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Quando si invia una richiesta Supporto tecnico Microsoft (o si risoluzione del problema che interessa terze parti), condividere la versione non corretta del comando che si vuole eseguire. Ciò garantisce che la firma di accesso condiviso non sia condivisa accidentalmente con nessuno. È possibile trovare la versione con modifiche all'inizio del file di log.

## <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà tutti gli errori `UPLOADFAILED` con stato dal `04dc9ca9-158f-7945-5933-564021086c79` log:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Visualizzare e riprendere i processi

Ogni operazione di trasferimento creerà un processo di AzCopy. Usare il comando seguente per visualizzare la cronologia dei processi:

```
azcopy jobs list
```

Per visualizzare le statistiche del processo, usare il comando seguente:

```
azcopy jobs show <job-id>
```

Per filtrare i trasferimenti in base allo stato, usare il comando seguente:

```
azcopy jobs show <job-id> --with-status=Failed
```

Usare il comando seguente per riprendere un processo non riuscito/annullato. Questo comando usa il relativo identificatore insieme al token di firma di accesso condiviso perché non è persistente per motivi di sicurezza:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Racchiudere gli argomenti di percorso, ad esempio il token di firma di accesso condiviso, tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

Quando si riprende un processo, AzCopy esamina il file del piano di processo. Il file di piano elenca tutti i file identificati per l'elaborazione al momento della creazione del processo. Quando si riprende un processo, AzCopy tenterà di trasferire tutti i file elencati nel file di piano che non sono già stati trasferiti.

## <a name="change-the-location-of-plan-files"></a>Modificare il percorso dei file di piano

Usare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> In un prompt dei comandi usare:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Usare `azcopy env` per controllare il valore corrente di questa variabile. Se il valore è vuoto, i file di piano vengono scritti nel percorso predefinito.

## <a name="change-the-location-of-log-files"></a>Modificare il percorso dei file di log

Usare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> In un prompt dei comandi usare:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Usare `azcopy env` per controllare il valore corrente di questa variabile. Se il valore è vuoto, i log vengono scritti nel percorso predefinito.

## <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log

Per impostazione predefinita, il livello di log di AzCopy è impostato su `INFO` . Se si vuole ridurre il livello di dettaglio del log per risparmiare spazio su disco, sovrascrivere questa impostazione usando ``--log-level`` l'opzione . 

I livelli di log disponibili `NONE` sono: `DEBUG` , , , , , e `INFO` `WARNING` `ERROR` `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Rimuovere i file di piano e di log

Se si desidera rimuovere tutti i file di piano e di log dal computer locale per risparmiare spazio su disco, usare il `azcopy jobs clean` comando .

Per rimuovere i file di piano e di log associati a un solo processo, usare `azcopy jobs rm <job-id>` . Sostituire il `<job-id>` segnaposto in questo esempio con l'ID del processo.

## <a name="see-also"></a>Vedi anche

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
