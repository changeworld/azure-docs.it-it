---
title: azcopy login | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503236"
---
# <a name="azcopy-login"></a>azcopy login

Accede a Azure Active Directory per accedere Archiviazione di Azure risorse.

## <a name="synopsis"></a>Riepilogo

Accedere a Azure Active Directory accedere Archiviazione di Azure risorse.

Per essere autorizzati all'account Archiviazione di Azure, è  necessario assegnare il ruolo Collaboratore dati BLOB di archiviazione all'account utente nel contesto dell'account di archiviazione, del gruppo di risorse padre o della sottoscrizione padre.

Questo comando memorizza nella cache le informazioni di accesso crittografate per l'utente corrente usando i meccanismi predefiniti del sistema operativo.

> [!IMPORTANT]
> Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Prendere in considerazione la possibilità di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili appaiano nella cronologia, è possibile usare uno script per richiedere all'utente le credenziali e impostare la variabile di ambiente.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Esercitazione: Eseguire la migrazione dei dati locali in una risorsa di archiviazione nel cloud con AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Trasferire dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="examples"></a>Esempio

Accedere in modo interattivo con l'ID tenant AAD predefinito impostato su common:

```azcopy
azcopy login
```

Accedere in modo interattivo con un ID tenant specificato:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Accedere usando l'identità assegnata dal sistema di una macchina virtuale (VM):

```azcopy
azcopy login --identity
```

Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID client dell'identità del servizio:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID oggetto dell'identità del servizio:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID risorsa dell'identità del servizio:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Accedere come entità servizio usando un segreto client: impostare la variabile di ambiente AZCOPY_SPA_CLIENT_SECRET sul segreto client per l'autenticazione dell'entità servizio basata su segreto.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Accedere come entità servizio usando un certificato e la password:

Impostare la variabile di ambiente AZCOPY_SPA_CERT_PASSWORD la password del certificato per l'autenticazione dell'entità servizio basata su certificato:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Considerare `/path/to/my/cert` come percorso di un file PEM o PKCS12. AzCopy non raggiunge l'archivio certificati di sistema per ottenere il certificato.

`--certificate-path` è obbligatorio quando si esegue l'autenticazione basata su certificati dell'entità servizio.

## <a name="options"></a>Opzioni

**--aad-endpoint** string Stringa Azure Active Directory endpoint da usare. Il valore predefinito ( https://login.microsoftonline.com) è corretto per il cloud di Azure globale. Impostare questo parametro durante l'autenticazione in un cloud nazionale. Non necessario per l'identità del servizio gestita.

**--application-id string** ID applicazione dell'identità assegnata dall'utente. Obbligatorio per l'autenticazione dell'entità servizio.

**--certificate-path string** Percorso del certificato per l'autenticazione SPN. Obbligatorio per l'autenticazione basata su certificati dell'entità servizio.

**--help**   help per il `azcopy login` comando.

**--identity**   Accedere usando l'identità della macchina virtuale, nota anche come identità del servizio gestito.

**--identity-client-id** string ID client dell'identità assegnata dall'utente.

**--identity-object-id string** ID oggetto dell'identità assegnata dall'utente.

**--identity-resource-id** string ID risorsa dell'identità assegnata dall'utente.

**--service-principal**   Accedere tramite il nome dell'entità servizio (SPN) usando un certificato o un segreto. Il segreto client o la password del certificato deve essere inserita nella variabile di ambiente appropriata. Digitare AzCopy env per visualizzare i nomi e le descrizioni delle variabili di ambiente.

**--tenant-id** string L Azure Active Directory ID tenant da usare per l'accesso interattivo al dispositivo OAuth.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.|
|--output-type string|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
