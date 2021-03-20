---
title: Elimina con applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Viene fornita una guida per eseguire il comando Delete dello strumento di snapshot coerente applicazione Azure che è possibile utilizzare con Azure NetApp Files.
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
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632909"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Elimina mediante lo strumento di snapshot coerente con applicazione Azure (anteprima)

Questo articolo fornisce una guida per l'esecuzione del comando Delete dello strumento di snapshot coerente applicazione Azure che è possibile usare con Azure NetApp Files.

## <a name="introduction"></a>Introduzione

Con il comando è possibile eliminare gli snapshot del volume e le voci del catalogo di database `azacsnap -c delete` .

> [!IMPORTANT]
> Gli snapshot creati meno di 10 minuti prima di eseguire questo comando non devono essere eliminati a causa della potenziale interferenza con la replica snapshot.

## <a name="command-options"></a>Opzioni del comando

Per il `-c delete` comando sono disponibili le opzioni seguenti:

- `--delete hana` Se usato con le opzioni `--hanasid <SID>` e `--hanabackupid <HANA backup id>` eliminerà le voci dal catalogo di backup SAP Hana corrispondente ai criteri.

- `--delete storage` Se usato con l'opzione `--snapshot <snapshot name>` , eliminerà lo snapshot dal sistema di archiviazione back-end.

- `--delete sync` Se usato con le opzioni `--hanasid <SID>` e `--hanabackupid <HANA backup id>` ottiene il nome dello snapshot di archiviazione dal catalogo di backup per l'oggetto `<HANA backup id>` , quindi Elimina la voce nel catalogo di backup _e_ lo snapshot da uno qualsiasi dei volumi contenenti lo snapshot denominato.

- `--delete sync` Se utilizzata con `--snapshot <snapshot name>` , verificherà la presenza di voci nel catalogo di backup per `<snapshot name>` , ottiene l'ID di backup SAP Hana ed Elimina sia la voce nel catalogo di backup _che_ lo snapshot da uno qualsiasi dei volumi contenenti lo snapshot denominato.

- `[--force]` opzionale *Usare con cautela*.  Questa operazione forzerà l'eliminazione senza richiedere conferma.

- `[--configfile <config filename>]` è un parametro facoltativo che consente i nomi dei file di configurazione personalizzati.

### <a name="delete-a-snapshot-using-sync-option"></a>Eliminare uno snapshot utilizzando l' `sync` opzione '

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Verifica la presenza di voci nel catalogo di backup per il SAP HANA ID di backup 157979797979, ottiene il nome dello snapshot di archiviazione ed Elimina sia la voce nel catalogo di backup che lo snapshot da tutti i volumi contenenti lo snapshot denominato.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Verifica la presenza di eventuali voci nel catalogo di backup per lo snapshot denominato hana_hourly .2020-01 -22 _2358, ottiene l'ID di backup SAP HANA ed Elimina sia la voce nel catalogo di backup che lo snapshot da uno dei volumi contenenti lo snapshot denominato.

### <a name="delete-a-snapshot-using-hana-option"></a>Eliminare uno snapshot utilizzando l' `hana` opzione '

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Elimina il SAP HANA ID di backup 157979797979 dal catalogo di backup per SID H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Eliminare uno snapshot utilizzando l' `storage` opzione '

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Elimina lo snapshot da tutti i volumi che contengono uno snapshot denominato hana_hourly .2020-01 -22 _2358.

**Output con l' `--delete storage` opzione**

All'utente viene richiesto di confermare l'eliminazione.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

È possibile evitare la conferma dell'utente, usando il parametro facoltativo `--force` come indicato di seguito:

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere i dettagli dello snapshot](azacsnap-cmd-ref-details.md)
- [Eseguire un backup](azacsnap-cmd-ref-backup.md)
