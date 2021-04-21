---
title: Scegliere come autorizzare l'accesso ai dati della coda con l'interfaccia della riga di comando di Azure
titleSuffix: Azure Storage
description: Specificare come autorizzare le operazioni sui dati della coda con l'interfaccia della riga di comando di Azure. È possibile autorizzare le operazioni sui dati usando Azure AD credenziali, con la chiave di accesso dell'account o con un token di firma di accesso condiviso.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b3ac012da97194134f58d061dd9d84e945db554
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774468"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Scegliere come autorizzare l'accesso ai dati della coda con l'interfaccia della riga di comando di Azure

Archiviazione di Azure fornisce estensioni per l'interfaccia della riga di comando di Azure che consentono di specificare come autorizzare le operazioni sui dati della coda. È possibile autorizzare le operazioni sui dati nei modi seguenti:

- Con un'Azure Active Directory di sicurezza (Azure AD). Microsoft consiglia di usare Azure AD credenziali per una maggiore sicurezza e facilità d'uso.
- Con la chiave di accesso dell'account o un token di firma di accesso condiviso.

## <a name="specify-how-data-operations-are-authorized"></a>Specificare la modalità di autorizzazione delle operazioni sui dati

I comandi dell'interfaccia della riga di comando di Azure per la lettura e la scrittura dei dati della coda includono il parametro `--auth-mode` facoltativo. Specificare questo parametro per indicare come deve essere autorizzata un'operazione sui dati:

- Impostare il `--auth-mode` parametro su per accedere usando `login` un'Azure AD di sicurezza (scelta consigliata).
- Impostare il parametro sul valore legacy per tentare di recuperare la chiave `--auth-mode` di accesso dell'account da usare per `key` l'autorizzazione. Se si omette il parametro , anche l'interfaccia della riga di comando di Azure tenta `--auth-mode` di recuperare la chiave di accesso.

Per usare il parametro , assicurarsi di aver installato l'interfaccia della riga di comando di `--auth-mode` Azure versione 2.0.46 o successiva. Eseguire `az --version` per controllare la versione installata.

> [!NOTE]
> Quando un account di archiviazione viene bloccato con [](/rest/api/storagerp/storageaccounts/listkeys) un Azure Resource Manager **di** sola lettura, l'operazione Elenca chiavi non è consentita per tale account di archiviazione. **List Keys** è un'operazione POST e tutte le operazioni POST vengono impedite quando viene configurato un blocco **ReadOnly** per l'account. Per questo motivo, quando l'account è bloccato con un blocco **ReadOnly,** gli utenti che non possiedono già le chiavi dell'account devono usare Azure AD credenziali per accedere ai dati della coda.

> [!IMPORTANT]
> Se si omette il parametro o lo si imposta su , l'interfaccia della riga di comando di Azure tenta di `--auth-mode` usare la chiave di accesso `key` dell'account per l'autorizzazione. In questo caso, Microsoft consiglia di fornire la chiave di accesso nel comando o nella variabile `AZURE_STORAGE_KEY` di ambiente . Per altre informazioni sulle variabili di ambiente, vedere la sezione impostare le variabili di ambiente per [i parametri di autorizzazione.](#set-environment-variables-for-authorization-parameters)
>
> Se non si specifica la chiave di accesso, l'interfaccia della riga di comando di Azure tenta di chiamare il provider di risorse Archiviazione di Azure per recuperarla per ogni operazione. L'esecuzione di molte operazioni sui dati che richiedono una chiamata al provider di risorse può comportare la limitazione. Per altre informazioni sui limiti del provider di risorse, vedere Obiettivi di scalabilità e prestazioni per [Archiviazione di Azure provider di risorse.](../common/scalability-targets-resource-provider.md)

## <a name="authorize-with-azure-ad-credentials"></a>Autorizzare con Azure AD credenziali

Quando si accede all'interfaccia della riga di comando di Azure Azure AD, viene restituito un token di accesso OAuth 2.0. Tale token viene usato automaticamente dall'interfaccia della riga di comando di Azure per autorizzare le operazioni dati successive sull'archiviazione code. Per le operazioni supportate, non è più necessario passare un chiave dell'account o un token di firma di accesso condiviso con il comando.

È possibile assegnare le autorizzazioni per accodare i dati a un Azure AD di sicurezza tramite il controllo degli accessi in base al ruolo di Azure. Per altre informazioni sui ruoli di Azure in Archiviazione di Azure, vedere Gestire i diritti di accesso ai Archiviazione di Azure dati con il controllo degli accessi in base al ruolo [di Azure.](../common/storage-auth-aad-rbac-portal.md)

### <a name="permissions-for-calling-data-operations"></a>Autorizzazioni per chiamare operazioni sui dati

Le Archiviazione di Azure sono supportate per le operazioni sui dati della coda. Le operazioni che è possibile chiamare dipendono dalle autorizzazioni concesse all'entità Azure AD di sicurezza con cui si accede all'interfaccia della riga di comando di Azure. Le autorizzazioni per le code vengono assegnate tramite il controllo degli accessi in base al ruolo di Azure. Ad esempio, se è stato assegnato il **ruolo** di lettore dei dati della coda di archiviazione, è possibile eseguire comandi di scripting che leggono i dati da una coda. Se è stato assegnato il ruolo **Collaboratore** ai dati della coda di archiviazione, è possibile eseguire comandi di scripting che leggono, scrivono o eliminano una coda o i dati in essi contenuti.

Per informazioni dettagliate sulle autorizzazioni necessarie per ogni Archiviazione di Azure in una coda, vedere Chiamare operazioni di archiviazione [con token OAuth.](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Esempio: Autorizzare un'operazione per creare una coda con Azure AD credenziali

L'esempio seguente illustra come creare una coda dall'interfaccia della riga di comando di Azure usando Azure AD credenziali. Per creare la coda, è necessario accedere all'interfaccia della riga di comando di Azure e sono necessari un gruppo di risorse e un account di archiviazione.

1. Prima di creare la coda, assegnare il ruolo [Collaboratore](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) dati coda di archiviazione a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati sull'account di archiviazione. Per altre informazioni sull'assegnazione dei ruoli di Azure, vedere Usare il portale di Azure per assegnare un ruolo di Azure per l'accesso ai [dati blob e coda.](../common/storage-auth-aad-rbac-portal.md)

    > [!IMPORTANT]
    > La propagazione delle assegnazioni dei ruoli può richiedere alcuni minuti.

1. Chiamare il comando con il parametro impostato su per creare la coda usando [`az storage queue create`](/cli/azure/storage/queue#az_storage_queue_create) `--auth-mode` Azure AD `login` credenziali. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizzare con la chiave di accesso dell'account

Se si possiede la chiave dell'account, è possibile chiamare qualsiasi operazione Archiviazione di Azure dati. In generale, l'uso della chiave dell'account è meno sicuro. Se la chiave dell'account è compromessa, tutti i dati nell'account potrebbero essere compromessi.

Nell'esempio seguente viene illustrato come creare una coda usando la chiave di accesso dell'account. Specificare la chiave dell'account e specificare `--auth-mode` il parametro con il valore `key` :

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizzare con un token di firma di accesso condiviso

Se si dispone di un token di firma di accesso condiviso, è possibile chiamare le operazioni sui dati consentite dalla firma di accesso condiviso. L'esempio seguente illustra come creare una coda usando un token di firma di accesso condiviso:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Impostare le variabili di ambiente per i parametri di autorizzazione

È possibile specificare i parametri di autorizzazione nelle variabili di ambiente per evitare di includerli in ogni chiamata a un'Archiviazione di Azure di dati. Nella tabella seguente vengono descritte le variabili di ambiente disponibili.

| Variabile di ambiente | Descrizione |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | nome dell'account di archiviazione. Questa variabile deve essere usata insieme alla chiave dell'account di archiviazione o a un token di firma di accesso condiviso. Se nessuno dei due è presente, l'interfaccia della riga di comando di Azure tenta di recuperare la chiave di accesso dell'account di archiviazione usando l'account Azure AD autenticazione. Se viene eseguito un numero elevato di comandi contemporaneamente, è possibile che venga raggiunto Archiviazione di Azure limite di limitazione del provider di risorse. Per altre informazioni sui limiti del provider di risorse, vedere Obiettivi di scalabilità e prestazioni per [Archiviazione di Azure provider di risorse.](../common/scalability-targets-resource-provider.md) |
| **AZURE_STORAGE_KEY** | Chiave dell'account di archiviazione. Questa variabile deve essere usata insieme al nome dell'account di archiviazione. |
| **AZURE_STORAGE_CONNECTION_STRING** | Stringa di connessione che include la chiave dell'account di archiviazione o un token di firma di accesso condiviso. Questa variabile deve essere usata insieme al nome dell'account di archiviazione. |
| **AZURE_STORAGE_SAS_TOKEN** | Token di firma di accesso condiviso. Questa variabile deve essere usata insieme al nome dell'account di archiviazione. |
| **AZURE_STORAGE_AUTH_MODE** | Modalità di autorizzazione con cui eseguire il comando. I valori consentiti `login` sono (scelta consigliata) o `key` . Se si specifica , l'interfaccia della riga di comando di `login` Azure Azure AD credenziali per autorizzare l'operazione sui dati. Se si specifica la modalità legacy, l'interfaccia della riga di comando di Azure tenta di eseguire una query per la chiave di accesso dell'account e di autorizzare il `key` comando con la chiave. |

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo di Azure per l'accesso ai dati di BLOB e code](../common/storage-auth-aad-rbac-cli.md)
- [Autorizzare l'accesso ai dati di BLOB e code con identità gestite per le risorse di Azure](../common/storage-auth-aad-msi.md)
