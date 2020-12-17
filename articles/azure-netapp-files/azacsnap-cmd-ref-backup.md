---
title: Eseguire il backup con applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Viene fornita una guida per eseguire il comando backup dello strumento di snapshot coerente applicazione Azure che è possibile utilizzare con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1051859d514c77bad1aa5f14becc2218a923df44
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632993"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Eseguire il backup con applicazione Azure strumento snapshot coerente (anteprima)

Questo articolo fornisce una guida per l'esecuzione del comando di backup dello strumento di snapshot coerente applicazione Azure che è possibile usare con Azure NetApp Files.

## <a name="introduction"></a>Introduzione

Per eseguire un backup basato su snapshot di archiviazione `azacsnap -c backup` , usare il comando.  Questo comando esegue l'orchestrazione di uno snapshot di archiviazione coerente con il database sui volumi di dati e uno snapshot di archiviazione (senza alcuna configurazione di coerenza del database) sugli altri volumi.  

Per i volumi di dati `azacsnap` , il database verrà preparato per uno snapshot di archiviazione, quindi verrà preso in considerazione lo snapshot di archiviazione per tutti i volumi configurati, infine verrà indicato al database che lo snapshot è completo.  Consente inoltre di gestire le voci di database che registrano l'attività di backup di snapshot, ad esempio SAP HANA catalogo di backup.

## <a name="command-options"></a>Opzioni del comando

Il `-c backup` comando accetta gli argomenti seguenti:

- `--volume=` tipo di volume da snapshot, questo parametro può contenere `data` o `other`
  - `data` snapshot dei volumi all'interno della stanza DataVolume del file di configurazione.
  - `other` snapshot dei volumi all'interno della strofa otherVolume del file di configurazione.
  
  > [!NOTE]
  > Creando un file di configurazione separato con il volume di avvio come otherVolume, è possibile che gli `boot` snapshot vengano presi in una pianificazione completamente diversa (ad esempio, ogni giorno).

- `--prefix=` prefisso dello snapshot del cliente per il nome dello snapshot. Questo parametro ha due scopi. Per prima cosa, è necessario specificare un nome univoco per il raggruppamento degli snapshot. In secondo luogo, per determinare il `--retention` numero di snapshot di archiviazione conservati per l'oggetto specificato `--prefix` .

    > [!IMPORTANT]
    > Sono consentiti solo caratteri alfanumerici ("A-Z, a-z, 0-9"), caratteri di sottolineatura ("_") e trattini ("-").

- `--retention` numero di snapshot dell'oggetto definito `--prefix` da mantenere. Eventuali snapshot aggiuntivi vengono rimossi dopo che è stato eseguito un nuovo snapshot `--prefix` .

- `--trim` disponibile per SAP HANA V2 e versioni successive, questa opzione mantiene il catalogo di backup e i backup del log e del catalogo del disco. Il numero di voci da memorizzare nel catalogo di backup è determinato dall' `--retention` opzione precedente ed Elimina le voci meno recenti per il prefisso definito ( `--prefix` ) dal catalogo di backup e il backup dei log fisici correlati. Elimina inoltre le voci di backup del log precedenti alla voce di backup non del log meno recente. Questa operazione consente di impedire che i backup del log utilizzino tutto lo spazio su disco disponibile.

  > [!NOTE]
  > Il comando di esempio seguente manterrà 9 snapshot di archiviazione e assicurerà che il catalogo di backup venga tagliato continuamente in modo da corrispondere ai 9 snapshot di archiviazione conservati.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` parametro facoltativo che definisce il metodo di crittografia utilizzato per comunicare con SAP HANA, `openssl` o `commoncrypto` . Se definito, il `azacsnap -c backup` comando prevede di trovare due file nella stessa directory. questi file devono essere denominati dopo il SID corrispondente. Vedere [uso di SSL per la comunicazione con SAP Hana](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). Nell'esempio seguente viene preso uno `hana` snapshot del tipo con un prefisso `hana_TEST` e si manterrà la `9` comunicazione con SAP HANA tramite SSL ( `openssl` ).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` è un parametro facoltativo che consente i nomi dei file di configurazione personalizzati.

## <a name="snapshot-backups-are-fast"></a>I backup di snapshot sono veloci

La durata di un backup di snapshot è indipendente dalle dimensioni del volume, con un volume di 10 TB che viene bloccato entro la stessa ora approssimativa di un volume di 10 GB.  

I fattori principali che influiscono sul tempo di esecuzione complessivo sono il numero di volumi da creare come snapshot e tutte le modifiche apportate al `--retention` parametro (dove una riduzione può aumentare il tempo di esecuzione quando vengono rimossi gli snapshot in eccesso).

Nella configurazione di esempio precedente (per **istanze large di Azure**) gli snapshot per i due volumi hanno richiesto meno di 5 secondi per il completamento. Per **Azure NetApp files**, gli snapshot per i due volumi richiederanno circa 60 secondi.

> [!NOTE]
> Se l'oggetto `--retention` è significativamente ridotto rispetto all' `azacsnap` esecuzione precedente (ad esempio, da `--retention 50` a `--retention 5` ), il tempo impiegato aumenterà in base alla `azacsnap` necessità di rimuovere gli snapshot aggiuntivi.

## <a name="example-with-data-parameter"></a>Esempio con `data` parametro

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

Il comando non viene restituito alla console, ma scrive in un file di log, in un file di risultati e in `/var/log/messages` .

Il *file di log* è costituito dal nome del comando + l'opzione-c + il nome file config. Per impostazione predefinita, un nome file di log per un' `-c backup` esecuzione con un nome file di configurazione predefinito `azacsnap-backup-azacsnap.log` .

Il file dei *risultati* ha lo stesso nome di base del file di log, con il `.result` suffisso, ad esempio, `azacsnap-backup-azacsnap.result` che contiene l'output seguente:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

Il `/var/log/messages` file contiene lo stesso output del `.result` file. Vedere l'esempio seguente (Esegui come radice):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Esempio con `other` parametro

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

Il comando non viene restituito alla console, ma scrive solo in un file di log.  _Non scrive in_ un file di risultati o `/var/log/messages` .

Il *file di log* è costituito dal nome del comando + l'opzione-c + il nome file config. Per impostazione predefinita, un nome file di log per un' `-c backup` esecuzione con un nome file di configurazione predefinito `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Esempio con `other` parametro (per il backup del sistema operativo host)

> [!NOTE]
> L'uso di un altro file di configurazione ( `--configfile bootVol.json` ) che contiene solo i volumi di avvio.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

Il comando non viene restituito alla console, ma scrive solo in un file di log.  _Non scrive in_ un file di risultati o `/var/log/messages` .

Il nome del *file di log* in questo esempio è `azacsnap-backup-bootVol.log` .

> [!NOTE]
> Il nome del file di log è costituito da "(nome comando-( `-c` opzione)-(nome file di configurazione)".  Se, ad esempio, si utilizza l' `-c backup` opzione con un nome di file di log `h80.json` , verrà chiamato il file di log `azacsnap-backup-h80.log` .  In alternativa, se `-c test` si usa l'opzione con lo stesso file di configurazione, verrà chiamato il file di log `azacsnap-test-h80.log` .

- Tipo di istanza large di HANA: sono presenti due valori validi con `TYPEI` o che `TYPEII` dipendono dall'unità di istanze large di Hana.
- Vedere [SKU disponibili per le istanze large di Hana](/azure/virtual-machines/workloads/sap/hana-available-skus) per confermare gli SKU disponibili.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere i dettagli dello snapshot](azacsnap-cmd-ref-details.md)
- [Eliminare gli snapshot](azacsnap-cmd-ref-delete.md)
