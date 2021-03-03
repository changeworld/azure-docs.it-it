---
title: Creare e gestire le credenziali per le analisi
description: Informazioni sui passaggi per creare e gestire le credenziali in Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 1857eab485e8651c05959f82cf11e69b6353c575
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673518"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Credenziali per l'autenticazione dell'origine in Azure

Questo articolo descrive come è possibile creare le credenziali in Azure. Queste credenziali salvate consentono di riutilizzare e applicare rapidamente le informazioni di autenticazione salvate alle analisi dell'origine dati.

## <a name="prerequisites"></a>Prerequisiti

- Un insieme di credenziali delle chiavi di Azure. Per informazioni su come crearne uno, vedere [Guida introduttiva: creare un insieme di credenziali delle chiavi usando il portale di Azure](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introduzione

Le credenziali sono informazioni di autenticazione che possono essere usate da Azure per autenticare le origini dati registrate. È possibile creare un oggetto credenziale per diversi tipi di scenari di autenticazione, ad esempio l'autenticazione di base che richiede nome utente e password. Le credenziali acquisiscono informazioni specifiche necessarie per eseguire l'autenticazione, in base al tipo di metodo di autenticazione scelto. Le credenziali usano i segreti di Azure Key Vault esistenti per recuperare le informazioni di autenticazione riservate durante il processo di creazione delle credenziali.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Usare l'identità gestita di competenza per configurare le analisi

Se si usa l'identità gestita di competenza per configurare le analisi, non è necessario creare in modo esplicito una credenziale e collegare l'insieme di credenziali delle chiavi a competenza per archiviarle. Per istruzioni dettagliate sull'aggiunta dell'identità gestita di base per l'accesso all'analisi delle origini dati, vedere le sezioni di autenticazione specifiche dell'origine dati di seguito:

- [Archiviazione BLOB di Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Database SQL di Azure](register-scan-azure-sql-database.md)
- [Istanza gestita di database SQL di Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Creare connessioni di Azure Key Vault nell'account Azure

Prima di poter creare una credenziale, associare prima di tutto una o più istanze di Azure Key Vault esistenti con l'account Azure.

1. Dal [portale di Azure](https://portal.azure.com)selezionare il proprio account Azure. Passare al **centro di gestione** , quindi passare a **credenziali**.

2. Nella pagina **credenziali** selezionare **gestisci connessioni Key Vault**.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Gestisci connessioni Azure Key Vault":::

3. Selezionare **+ nuovo** dalla pagina Gestisci connessioni Key Vault.

4. Fornire le informazioni necessarie, quindi selezionare **Crea**.

5. Verificare che il Key Vault sia stato associato correttamente all'account Azure, come illustrato nell'esempio seguente:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Consente di visualizzare Azure Key Vault le connessioni da confermare.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Concessione dell'accesso all'identità gestita per il proprio Azure Key Vault

1. Passare alla Azure Key Vault.

2. Selezionare la pagina **criteri di accesso** .

3. Selezionare **Aggiungi criterio di accesso**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Aggiungere la competenza MSI a AKV":::

4. Nell'elenco a discesa autorizzazioni per i **segreti** selezionare **Get** ed **Elenca** autorizzazioni.

5. Per **Seleziona entità** scegliere l'identità gestita di competenza. È possibile cercare l'identità del servizio gestito di competenza usando il nome dell'istanza di competenza **o** l'ID dell'applicazione di identità gestita. Attualmente non sono supportate le identità composte (nome identità gestita + ID applicazione).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Aggiungere un criterio di accesso":::

6. Selezionare **Aggiungi**.

7. Selezionare **Save (Salva** ) per salvare i criteri di accesso.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Salva criteri di accesso":::

## <a name="create-a-new-credential"></a>Crea una nuova credenziale

Questi tipi di credenziali sono supportati in ambito:

- Autenticazione di base: è possibile aggiungere la **password** come segreto in Key Vault.
- Entità servizio: aggiungere la **chiave dell'entità servizio** come segreto in Key Vault.
- Autenticazione SQL: è possibile aggiungere la **password** come segreto in Key Vault.
- Chiave dell'account: aggiungere la **chiave dell'account** come segreto in Key Vault.
- Ruolo ARN: per un'origine dati Amazon S3, aggiungere il **ruolo ARN** in AWS. 

Per altre informazioni, vedere [aggiungere un segreto per Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) e [creare un nuovo ruolo AWS per la competenza](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview).

Dopo l'archiviazione dei segreti nell'insieme di credenziali delle chiavi:

1. In Azure competenza passare alla pagina credenziali.

2. Creare la nuova credenziale selezionando **+ nuovo**.

3. Fornire le informazioni necessarie. Selezionare il **metodo di autenticazione** e una **connessione Key Vault** da cui selezionare un segreto.

4. Una volta completati tutti i dettagli, selezionare **Crea**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nuove credenziali":::

5. Verificare che la nuova credenziale venga visualizzata nella visualizzazione elenco ed è pronta per l'uso.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Visualizza credenziali":::

## <a name="manage-your-key-vault-connections"></a>Gestire le connessioni di Key Vault

1. Cerca/trova Key Vault connessioni per nome

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Cercare l'insieme di credenziali delle chiavi":::

2. Eliminare una o più connessioni di Key Vault

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Elimina Key Vault":::

## <a name="manage-your-credentials"></a>Gestire le credenziali

1. Cerca/trova le credenziali in base al nome.
  
2. Selezionare e apportare aggiornamenti a una credenziale esistente.

3. Eliminare una o più credenziali.

## <a name="next-steps"></a>Passaggi successivi

[Creare un set di regole di analisi](create-a-scan-rule-set.md)
