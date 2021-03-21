---
title: Backup e ripristino per Database di Azure per i gruppi di server PostgreSQL Hyperscale
description: Backup e ripristino per Database di Azure per i gruppi di server PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686700"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Eseguire il backup e il ripristino dei gruppi di server di scalabilità PostgreSQL abilitati per Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Quando si esegue il backup o il ripristino del gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc, viene eseguito il backup e/o il ripristino dell'intero set di database in tutti i nodi PostgreSQL del gruppo di server.

## <a name="take-a-manual-full-backup"></a>Eseguire un backup completo manuale

Per eseguire un backup completo dell'intera cartella di dati e log del gruppo di server, eseguire il comando seguente:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Dove:
- __nome__ indica il nome di un backup
- __nome-server__ indica un gruppo di server
- __No-wait__ indica che la riga di comando non attende il completamento del backup per poter continuare a usare questa finestra della riga di comando

Questo comando consente di coordinare un backup completo distribuito in tutti i nodi che costituiscono il gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc. In altre parole, eseguirà il backup di tutti i dati nel coordinatore e nei nodi di lavoro.

Ad esempio:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Al termine del backup, verranno restituiti l'ID, il nome, le dimensioni, lo stato e il timestamp del backup. Ad esempio:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` indica il fuso orario per l'ora in cui è stato effettuato il backup. In questo esempio "+ 00:00" indica l'ora UTC (UTC + 00 ore 00 minuti).

> [!NOTE]
> Non è ancora possibile:
> - Pianificare backup automatici
> - Mostra lo stato di avanzamento di un backup mentre viene eseguito

## <a name="list-backups"></a>Elenco dei backup

Per elencare i backup disponibili per il ripristino, eseguire il comando seguente:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Ad esempio:

```console
azdata arc postgres backup list --server-name postgres01
```

Restituisce un output simile al seguente:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

La colonna timestamp indica il punto nel tempo UTC in cui è stato effettuato il backup.

## <a name="restore-a-backup"></a>Ripristinare un backup
In questa sezione viene illustrato come eseguire un ripristino completo o un ripristino temporizzato. Quando si ripristina un backup completo, viene ripristinato l'intero contenuto del backup. Quando si esegue un ripristino temporizzato, viene ripristinato fino al punto nel tempo indicato. Qualsiasi transazione eseguita successivamente a questo momento non viene ripristinata.

### <a name="restore-a-full-backup"></a>Ripristinare un backup completo
Per ripristinare l'intero contenuto di un backup, eseguire il comando:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Dove:
- __backup-ID__ è l'ID del backup visualizzato nel comando list backup riportato sopra.
Verrà coordinata un'operazione di ripristino completo distribuito in tutti i nodi che costituiscono il gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc. In altre parole, ripristinerà tutti i dati nel coordinatore e nei nodi di lavoro.

#### <a name="examples"></a>Esempi:

__Ripristinare il gruppo di server postgres01 su se stesso:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Questa operazione è supportata solo per PostgreSQL versione 12 e successive.

__Ripristinare il gruppo di server postgres01 in un gruppo di server diverso postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Questa operazione è supportata per qualsiasi versione di PostgreSQL che inizia con la versione 11. Il gruppo di server di destinazione deve essere creato prima dell'operazione di ripristino, deve essere della stessa configurazione e deve utilizzare lo stesso PVC di backup del gruppo di server di origine.

Al termine dell'operazione di ripristino, verrà restituito un output simile al seguente alla riga di comando:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Non è ancora possibile:
> - Ripristinare un backup indicando il nome
> - Mostra lo stato di avanzamento di un'operazione di ripristino


### <a name="do-a-point-in-time-restore"></a>Eseguire un ripristino temporizzato

Per ripristinare un gruppo di server fino a un punto specifico, eseguire il comando:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Per leggere il formato generale del comando Restore, eseguire: `azdata arc postgres backup restore --help` .

Dove `time` è il momento in cui eseguire il ripristino. Specificare un timestamp o un numero e un suffisso ( `m` per minuti, `h` per ore, `d` per giorni o `w` per settimane). Ad esempio `1.5h` , torna indietro 90 minuti.

#### <a name="examples"></a>Esempi:
__Eseguire un ripristino temporizzato del gruppo di server postgres01 su se stesso:__

Non è ancora possibile eseguire il ripristino temporizzato di un gruppo di server su se stesso.

__Eseguire un ripristino temporizzato del gruppo di server postgres01 a un gruppo di server diverso postgres02 a un timestamp specifico:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Questo esempio Mostra come ripristinare il gruppo di server postgres02 lo stato in cui il gruppo di server postgres01 era l'8 dicembre 2020 alle 04:23:48.75 UTC. Si noti che "+ 00" indica il fuso orario del punto nel tempo indicato. Se non si indica un fuso orario, verrà utilizzato il fuso orario del client da cui si esegue l'operazione di ripristino.

Ad esempio:
- `2020-12-08 04:23:48.751326+00` viene interpretato come `2020-12-08 04:23:48.751326` UTC
- Se ci si trova nel fuso orario standard del Pacifico (PST = UTC + 08), `2020-12-08 04:23:48.751326` viene interpretato come `2020-12-08 12:23:48.751326` UTC. questa operazione è supportata per qualsiasi versione di PostgreSQL che inizia con la versione 11. Il gruppo di server di destinazione deve essere creato prima dell'operazione di ripristino e deve utilizzare lo stesso PVC di backup del gruppo di server di origine.


__Eseguire un ripristino temporizzato del gruppo di server postgres01 in un gruppo di server diverso postgres02 a un determinato periodo di tempo nel passato:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Questo esempio Mostra come ripristinare il gruppo di server postgres02 lo stato in cui postgres01 del gruppo di server è stato 22 minuti fa.
Questa operazione è supportata per qualsiasi versione di PostgreSQL che inizia con la versione 11. Il gruppo di server di destinazione deve essere creato prima dell'operazione di ripristino e deve utilizzare lo stesso PVC di backup del gruppo di server di origine.

> [!NOTE]
> Non è ancora possibile:
> - Mostra lo stato di avanzamento di un'operazione di ripristino

## <a name="delete-backups"></a>Eliminare i backup

Impossibile impostare la conservazione dei backup in anteprima. Tuttavia, è possibile eliminare manualmente i backup che non sono necessari.
Il comando generale per eliminare i backup è:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

dove:
- `--server-name` nome del gruppo di server da cui l'utente desidera eliminare un backup
- `--name` nome del backup da eliminare
- `-id`ID del backup da eliminare

> [!NOTE]
> `--name` e si escludono a `-id` vicenda.

Ad esempio:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

È possibile recuperare il nome e l'ID dei backup eseguendo il comando list backup come illustrato nel paragrafo precedente.

Per ulteriori informazioni sul comando DELETE, eseguire:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sul [ridimensionamento (aggiunta di nodi di lavoro)](scale-out-postgresql-hyperscale-server-group.md) al gruppo di server
- Per informazioni su come [aumentare o ridurre la scalabilità (aumento/riduzione della memoria/VCore)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) del gruppo di server
