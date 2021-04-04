---
title: Testare applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Viene illustrato come eseguire il comando di test del applicazione Azure strumento di snapshot coerente che è possibile utilizzare con Azure NetApp Files.
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632820"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Test applicazione Azure strumento di snapshot coerente (anteprima)

Questo articolo illustra come eseguire il comando di test del applicazione Azure strumento di snapshot coerente che è possibile usare con Azure NetApp Files.

## <a name="introduction"></a>Introduzione

Un test della configurazione viene eseguito con il `azacsnap -c test` comando.

## <a name="command-options"></a>Opzioni del comando

Per il `-c test` comando sono disponibili le opzioni seguenti:

- `--test hana`  Verifica la connessione all'istanza di SAP HANA.

- `--test storage` Verifica la comunicazione con l'interfaccia di archiviazione sottostante creando uno snapshot di archiviazione temporanea in tutti i volumi configurati `data` , quindi rimuoverli. 

- `--test all` eseguirà entrambi i `hana` `storage` test e in sequenza.

- `[--configfile <config filename>]` è un parametro facoltativo che consente i nomi dei file di configurazione personalizzati.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Verifica la connettività con SAP HANA `azacsnap -c test --test hana`

Questo comando controlla la connettività HANA per tutte le istanze di HANA nel file di configurazione. USA HDBuserstore per connettersi al SYSTEMDB e recupera le informazioni SID.

Per SSL, questo comando può assumere il seguente argomento facoltativo:

- `--ssl=` impone una connessione crittografata con il database e definisce il metodo di crittografia utilizzato per comunicare con SAP HANA, `openssl` o `commoncrypto` . Se definito, questo comando prevede di trovare due file nella stessa directory. questi file devono essere denominati dopo il SID corrispondente. Vedere [uso di SSL per la comunicazione con SAP Hana](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>Output del `azacsnap -c test --test hana` comando

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Verificare la connettività con archiviazione `azacsnap -c test --test storage`

Il `azacsnap` comando creerà uno snapshot per tutti i volumi di dati configurati per verificare che disponga dell'accesso corretto ai volumi per ogni istanza di SAP Hana. Viene creato uno snapshot temporaneo che viene quindi rimosso per ogni volume di dati per verificare l'accesso allo snapshot per ogni file system.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>Output del `azacsnap -c test --test storage` comando

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Per le istanze large di Azure, `azacsnap -c test --test storage` il comando estrapola la generazione dell'archiviazione e lo SKU HLI.  In base a queste informazioni vengono fornite informazioni aggiuntive sulla configurazione degli snapshot di avvio (vedere la riga che inizia con `Action:` output).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Passaggi successivi

- [Backup di snapshot con applicazione Azure strumento snapshot coerente](azacsnap-cmd-ref-backup.md)
