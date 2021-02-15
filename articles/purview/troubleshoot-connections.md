---
title: Risolvere i problemi relativi alle connessioni in Azure
description: Questo articolo illustra i passaggi per risolvere i problemi relativi alle connessioni in Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 8be5def6d4780fd8b2ab65c5c9e1104a869f6d5a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391593"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Risolvere i problemi relativi alle connessioni in Azure

Questo articolo descrive come risolvere gli errori di connessione durante la configurazione di analisi sulle origini dati in Azure.

## <a name="permission-the-credential-on-the-data-source"></a>Autorizzazione delle credenziali per l'origine dati

Se si usa un'identità gestita o un'entità servizio come metodo di autenticazione per le analisi, è necessario consentire a tali identità di accedere all'origine dati.

Sono disponibili istruzioni specifiche per ogni tipo di origine:

- [Archiviazione BLOB di Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Esplora dati di Azure](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Database SQL di Azure](register-scan-azure-sql-database.md)
- [Istanza gestita di database SQL di Azure](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Archiviare le credenziali nell'insieme di credenziali delle chiavi e usare il nome e la versione del segreto corretti

È anche necessario archiviare le credenziali nell'istanza di Azure Key Vault e usare il nome e la versione del segreto corretti.

Per verificarlo, attenersi alla procedura seguente:

1. Passare alla Key Vault.
1. Selezionare **Impostazioni** > **Segreti**.
1. Selezionare il segreto usato per l'autenticazione per l'origine dati per le analisi.
1. Selezionare la versione che si intende usare e verificare che la password o la chiave dell'account sia corretta facendo clic su **Mostra valore segreto**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Verificare le autorizzazioni per l'identità gestita di competenza nel Azure Key Vault

Verificare che siano state configurate le autorizzazioni corrette per l'identità gestita di competenza per accedere ai Azure Key Vault.

Per verificarlo, attenersi alla procedura seguente:

1. Passare all'insieme di credenziali delle chiavi e alla sezione **criteri di accesso**
1. Verificare che l'identità gestita di competenza venga visualizzata nella sezione *criteri di accesso correnti* con le autorizzazioni **Get** e **List** per i segreti almeno

Se non viene visualizzata l'identità gestita di competenza, seguire la procedura descritta in [creare e gestire le credenziali per](manage-credentials.md) l'aggiunta. 

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)
