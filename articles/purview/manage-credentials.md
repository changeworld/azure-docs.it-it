---
title: Creare e gestire le credenziali per le analisi
description: Questo articolo illustra i passaggi per creare e gestire le credenziali in Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 30004306d9ff44df04a26640a2bd7a09256fce25
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516691"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Credenziali per l'autenticazione dell'origine in Azure

Questo articolo descrive come creare le credenziali in Azure per riutilizzare rapidamente e applicare le informazioni di autenticazione salvate alle analisi dell'origine dati.

## <a name="prerequisites"></a>Prerequisiti

* Un insieme di credenziali delle chiavi di Azure. Per informazioni su come crearne uno, vedere [Guida introduttiva: creare un insieme di credenziali delle chiavi usando il portale di Azure](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Introduzione
Le credenziali sono informazioni di autenticazione che possono essere usate da Azure per autenticare le origini dati registrate. È possibile creare un oggetto credenziale per diversi tipi di scenari di autenticazione, ad esempio l'autenticazione di base che richiede nome utente e password, e acquisire le informazioni specifiche necessarie in base al tipo di metodo di autenticazione scelto. Le credenziali usano i segreti di Azure Key Vault esistenti per recuperare le informazioni di autenticazione riservate durante il processo di creazione delle credenziali.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Uso dell'identità gestita di competenza per configurare le analisi
Se si usa l'identità gestita di competenza per configurare le analisi, non è necessario creare in modo esplicito una credenziale e collegare l'insieme di credenziali delle chiavi a competenza per archiviarle. Per istruzioni dettagliate sull'aggiunta dell'identità gestita di base per l'accesso all'analisi delle origini dati, vedere le sezioni di autenticazione specifiche dell'origine dati di seguito:

- [Archiviazione BLOB di Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Database SQL di Azure](register-scan-azure-sql-database.md)
- [Istanza gestita di database SQL di Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Creare connessioni di Azure Key Vault nell'account Azure

Prima di poter creare una credenziale, è prima necessario associare una o più istanze di Azure Key Vault esistenti con l'account Azure.

1. Dal menu di navigazione di Azure per le competenze, passare al centro di gestione e quindi passare a credenziali.

2. Dalla barra dei comandi credenziali selezionare Gestisci connessioni Key Vault

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Gestione delle connessioni AKV":::

3. Selezionare + nuovo nel pannello Gestisci connessioni Key Vault 

4. Fornire le informazioni necessarie, quindi selezionare Crea

5. Verificare che il Key Vault sia stato associato correttamente all'account Azure.

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Visualizza connessioni AKV":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Concessione dell'accesso all'identità gestita per il proprio Azure Key Vault

Passare a Key Vault-> criteri di accesso-> aggiungere i criteri di accesso. Concedere l'autorizzazione Get nell'elenco a discesa autorizzazioni per i segreti e selezionare entità come identità del servizio gestito. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Aggiungere la competenza MSI a AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Aggiungere un criterio di accesso":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Salva criteri di accesso":::

## <a name="create-a-new-credential"></a>Crea una nuova credenziale

Tipo di credenziale attualmente supportato in ambito di competenza:
* Autenticazione di base: la **password** viene aggiunta come segreto nell'insieme di credenziali delle chiavi
* Entità servizio: la **chiave dell'entità servizio** viene aggiunta come segreto nell'insieme di credenziali delle chiavi 
* Autenticazione SQL: la **password** viene aggiunta come segreto nell'insieme di credenziali delle chiavi
* Chiave dell'account: la chiave dell' **account** viene aggiunta come segreto nell'insieme di credenziali delle chiavi

Per altre informazioni su come aggiungere segreti a un insieme di credenziali delle chiavi, vedere l'articolo relativo all'inserimento di Key Vault

Dopo aver archiviato i segreti nell'insieme di credenziali delle chiavi, creare la nuova credenziale selezionando + nuovo dalla barra dei comandi delle credenziali. Fornire le informazioni necessarie, inclusa la selezione del metodo di autenticazione e di un'istanza di Key Vault da cui selezionare un segreto. Una volta completati tutti i dettagli, fare clic su Crea.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nuove credenziali":::

Verificare che la nuova credenziale venga visualizzata nella visualizzazione elenco credenziali ed è pronta per l'uso

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Visualizza credenziali":::

## <a name="manage-your-key-vault-connections"></a>Gestire le connessioni di Key Vault

1. Cerca/trova Key Vault connessioni per nome

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Cercare l'insieme di credenziali delle chiavi":::

1. Eliminare una o più connessioni di Key Vault
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Elimina Key Vault":::

## <a name="manage-your-credentials"></a>Gestire le credenziali

1. Cerca/trova le credenziali per nome
  
2. Selezionare e apportare aggiornamenti a una credenziale esistente

3. Elimina una o più credenziali
