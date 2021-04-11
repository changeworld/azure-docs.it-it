---
title: Ambiti di crittografia per l'archiviazione BLOB
description: Gli ambiti di crittografia offrono la possibilità di gestire la crittografia a livello del contenitore o di un singolo BLOB. È possibile usare gli ambiti di crittografia per creare confini sicuri tra i dati che si trovano nello stesso account di archiviazione, ma appartenenti a clienti diversi.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640456"
---
# <a name="encryption-scopes-for-blob-storage"></a>Ambiti di crittografia per l'archiviazione BLOB

Gli ambiti di crittografia consentono di gestire la crittografia con una chiave con ambito limitato a un contenitore o a un singolo BLOB. È possibile usare gli ambiti di crittografia per creare confini sicuri tra i dati che si trovano nello stesso account di archiviazione, ma appartenenti a clienti diversi.

Per ulteriori informazioni sull'utilizzo degli ambiti di crittografia, vedere [creare e gestire gli ambiti di crittografia](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Funzionamento degli ambiti di crittografia

Per impostazione predefinita, un account di archiviazione viene crittografato con una chiave con ambito per l'intero account di archiviazione. Quando si definisce un ambito di crittografia, è necessario specificare una chiave che può avere come ambito un contenitore o un singolo BLOB. Quando l'ambito di crittografia viene applicato a un BLOB, il BLOB viene crittografato con tale chiave. Quando l'ambito di crittografia viene applicato a un contenitore, funge da ambito predefinito per i BLOB in tale contenitore, in modo che tutti i BLOB caricati in tale contenitore possano essere crittografati con la stessa chiave. Il contenitore può essere configurato in modo da applicare l'ambito di crittografia predefinito per tutti i BLOB nel contenitore o per consentire il caricamento di un singolo BLOB nel contenitore con un ambito di crittografia diverso da quello predefinito.

Le operazioni di lettura su un blob creato con un ambito di crittografia si verificano in modo trasparente, purché l'ambito di crittografia non sia disabilitato.

### <a name="key-management"></a>Gestione delle chiavi

Quando si definisce un ambito di crittografia, è possibile specificare se l'ambito è protetto con una chiave gestita da Microsoft o con una chiave gestita dal cliente archiviata in Azure Key Vault. Diversi ambiti di crittografia nello stesso account di archiviazione possono usare chiavi gestite da Microsoft o gestite dal cliente. È anche possibile cambiare il tipo di chiave usata per proteggere un ambito di crittografia da una chiave gestita dal cliente a una chiave gestita da Microsoft o viceversa, in qualsiasi momento. Per altre informazioni sulle chiavi gestite dal cliente, vedere [chiavi gestite dal cliente per la crittografia di archiviazione di Azure](../common/customer-managed-keys-overview.md). Per ulteriori informazioni sulle chiavi gestite da Microsoft, vedere [informazioni sulla gestione delle chiavi di crittografia](../common/storage-service-encryption.md#about-encryption-key-management).

Se si definisce un ambito di crittografia con una chiave gestita dal cliente, è possibile scegliere di aggiornare la versione della chiave in modo automatico o manuale. Se si sceglie di aggiornare automaticamente la versione della chiave, archiviazione di Azure controlla ogni giorno l'insieme di credenziali delle chiavi o il modulo di protezione hardware gestito per una nuova versione della chiave gestita dal cliente e aggiorna automaticamente la chiave alla versione più recente. Per ulteriori informazioni sull'aggiornamento della versione della chiave per una chiave gestita dal cliente, vedere [aggiornare la versione della chiave](../common/customer-managed-keys-overview.md#update-the-key-version).

Un account di archiviazione può avere fino a 10.000 ambiti di crittografia protetti con chiavi gestite dal cliente per le quali la versione della chiave viene aggiornata automaticamente. Se l'account di archiviazione ha già 10.000 ambiti di crittografia protetti con chiavi gestite dal cliente che vengono aggiornate automaticamente, la versione della chiave deve essere aggiornata manualmente per eventuali ambiti di crittografia aggiuntivi protetti con le chiavi gestite dal cliente.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Ambiti di crittografia per contenitori e BLOB

Quando si crea un contenitore, è possibile specificare un ambito di crittografia predefinito per i BLOB che vengono successivamente caricati in tale contenitore. Quando si specifica un ambito di crittografia predefinito per un contenitore, è possibile decidere come viene applicato l'ambito di crittografia predefinito:

- È possibile richiedere che tutti i BLOB caricati nel contenitore usino l'ambito di crittografia predefinito. In questo caso, ogni BLOB nel contenitore viene crittografato con la stessa chiave.
- È possibile consentire a un client di eseguire l'override dell'ambito di crittografia predefinito per il contenitore, in modo che sia possibile caricare un BLOB con un ambito di crittografia diverso dall'ambito predefinito. In questo caso, i BLOB nel contenitore possono essere crittografati con chiavi diverse.

La tabella seguente riepiloga il comportamento di un'operazione di caricamento di BLOB, a seconda della configurazione dell'ambito di crittografia predefinito per il contenitore:

| L'ambito di crittografia definito nel contenitore è... | Caricamento di un BLOB con l'ambito di crittografia predefinito... | Caricamento di un BLOB con un ambito di crittografia diverso da quello predefinito... |
|--|--|--|
| Un ambito di crittografia predefinito con sostituzioni consentite | Ha esito positivo | Ha esito positivo |
| Un ambito di crittografia predefinito con sostituzioni non consentito | Ha esito positivo | non riuscita |

È necessario specificare un ambito di crittografia predefinito per un contenitore al momento della creazione del contenitore.

Se non viene specificato alcun ambito di crittografia predefinito per il contenitore, è possibile caricare un BLOB usando qualsiasi ambito di crittografia definito per l'account di archiviazione. È necessario specificare l'ambito di crittografia nel momento in cui il BLOB viene caricato.

## <a name="disabling-an-encryption-scope"></a>Disabilitazione di un ambito di crittografia

Quando si disabilita un ambito di crittografia, le operazioni di lettura o scrittura successive eseguite con l'ambito di crittografia avranno esito negativo con codice di errore HTTP 403 (accesso negato). Se si riabilita l'ambito di crittografia, le operazioni di lettura e scrittura proseguiranno di nuovo normalmente.

Quando un ambito di crittografia è disabilitato, non viene più fatturato. Disabilitare gli ambiti di crittografia non necessari per evitare addebiti superflui.

Se l'ambito di crittografia è protetto con una chiave gestita dal cliente e si elimina la chiave nell'insieme di credenziali delle chiavi, i dati diventeranno inaccessibili. Assicurarsi di disabilitare anche l'ambito di crittografia per evitare che venga addebitato.

Tenere presente che le chiavi gestite dal cliente sono protette dall'eliminazione temporanea e dall'eliminazione della protezione nell'insieme di credenziali delle chiavi e una chiave eliminata è soggetta al comportamento definito da tali proprietà. Per ulteriori informazioni, vedere uno degli argomenti seguenti nella documentazione di Azure Key Vault:

- [Come usare la funzionalità di eliminazione temporanea con PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Come usare l'eliminazione temporanea con l'interfaccia della riga di comando](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Non è possibile eliminare un ambito di crittografia.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md)
- [Creare e gestire gli ambiti di crittografia](encryption-scope-manage.md)
- [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](../common/customer-managed-keys-overview.md)
- [Cos'è l'insieme di credenziali chiave di Azure?](../../key-vault/general/overview.md)