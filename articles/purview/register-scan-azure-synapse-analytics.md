---
title: Come analizzare Azure sinapsi Analytics
description: Questa guida descrive i dettagli su come eseguire l'analisi di Azure sinapsi Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 62ca32ab4e348e1488fbb87672e582436b91d05d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875010"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Registrare e analizzare analisi sinapsi di Azure

Questo articolo illustra come registrare e analizzare un'istanza di Azure sinapsi Analytics (in precedenza SQL DW) in ambito di competenza.

## <a name="supported-capabilities"></a>Funzionalità supportate

Azure sinapsi Analytics (noto in precedenza come SQL DW) supporta le analisi complete e incrementali per l'acquisizione dei metadati e dello schema. Le analisi classificano anche i dati automaticamente in base alle regole di classificazione personalizzate e di sistema.

### <a name="known-limitations"></a>Limitazioni note

Azure competenza non supporta l'analisi delle [visualizzazioni](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) in Azure sinapsi Analytics

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure
- Accesso alla rete tra l'account di competenza e Azure sinapsi Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

Esistono tre modi per configurare l'autenticazione per l'analisi delle sinapsi di Azure:

- Identità gestita
- Autenticazione SQL
- Entità servizio

    > [!Note]
    > Solo l'account di accesso dell'entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database `loginmanager` nel database master possono creare nuovi account di accesso. Circa 15 minuti dopo aver concesso l'autorizzazione, l'account Purview dovrebbe avere le autorizzazioni appropriate per analizzare le risorse.

### <a name="managed-identity-recommended"></a>Identità gestita (scelta consigliata) 
   
L'account di competenza ha una propria identità gestita, che è fondamentalmente il nome dell'ambito al momento della creazione. È necessario creare un utente Azure AD in Azure sinapsi Analytics (denominato in precedenza SQL DW) con il nome di identità gestita di tale ambito attenendosi ai prerequisiti e all'esercitazione su come [creare Azure ad utenti che usano Azure ad applicazioni](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Esempio di sintassi SQL per creare l'utente e concedere l'autorizzazione:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

L'autenticazione deve disporre dell'autorizzazione per ottenere i metadati per il database, gli schemi e le tabelle. Deve inoltre essere in grado di eseguire query sulle tabelle per campionare la classificazione. Si consiglia di assegnare `db_owner` l'autorizzazione all'identità.

### <a name="service-principal"></a>Entità servizio

Per utilizzare l'autenticazione basata su entità servizio per le analisi, è possibile utilizzarne una esistente o crearne una nuova. 

> [!Note]
> Se è necessario creare una nuova entità servizio, seguire questa procedura:
> 1. Passare al [portale di Azure](https://portal.azure.com).
> 1. Selezionare **Azure Active Directory** nel menu a sinistra.
> 1. Selezionare **Registrazioni per l'app**.
> 1. Selezionare **+ Registrazione nuova applicazione**.
> 1. Immettere un nome per l'**applicazione** (il nome dell'entità servizio).
> 1. Selezionare **Account solo in questa directory dell'organizzazione**.
> 1. Per l'URI di reindirizzamento selezionare **Web** e immettere un URL qualsiasi. Non deve necessariamente essere reale o funzionante.
> 1. Selezionare **Registra**.

È necessario ottenere l'ID applicazione e il segreto dell'entità servizio:

1. Passare all'entità servizio nel [portale di Azure](https://portal.azure.com)
1. Copiare il valore di **ID applicazione (client)** da **Panoramica** e quello di **Segreto client** da **Certificati e segreti**.
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ Genera/Importa** e immettere il **Nome** scelto e il **Valore** come **Segreto client** dell'entità servizio
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Concessione dell'accesso dell'entità servizio ad Azure sinapsi Analytics (in precedenza SQL DW)

Inoltre, è necessario creare un Azure AD utente in Azure sinapsi Analytics attenendosi ai prerequisiti e all'esercitazione su come [creare Azure ad utenti che usano Azure ad applicazioni](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Esempio di sintassi SQL per creare l'utente e concedere l'autorizzazione:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Purview richiede l'**ID applicazione (client)** e il **segreto client** per eseguire l'analisi.

### <a name="sql-authentication"></a>Autenticazione SQL

È possibile seguire le istruzioni in [creare un account di accesso](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) per creare un account di accesso per Azure sinapsi Analytics (in precedenza SQL DW) se non è già presente.

Quando il metodo di autenticazione selezionato è **l'autenticazione SQL**, è necessario ottenere la password e archiviare l'insieme di credenziali delle chiavi:

1. Ottenere la password per l'account di accesso SQL
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** e il **valore** come *password* per l'account di accesso SQL
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando la chiave per configurare l'analisi

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Registrare un'istanza di Azure sinapsi Analytics (in precedenza SQL DW)

Per registrare un nuovo server Azure sinapsi Analytics nella Data Catalog, seguire questa procedura:

1. Passare all'account Purview
1. Selezionare **Origini** nel riquadro di spostamento sinistro
1. Selezionare **Registra**
1. In **registra origini** selezionare **Azure sinapsi Analytics (in precedenza SQL DW)**
1. Selezionare **Continua**

Nella schermata **Register Sources (Azure sinapsi Analytics)** eseguire le operazioni seguenti:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
1. Scegliere come puntare all'account di archiviazione desiderato:
   1. Selezionare una **sottoscrizione di Azure**, selezionare la sottoscrizione appropriata nella casella di riepilogo a discesa **sottoscrizione di Azure** e il server appropriato nella casella di riepilogo a discesa **nome server** .
   1. In alternativa, è possibile selezionare **Immetti manualmente** e immettere un **nome del server**.
1. Selezionare **Fine** per completare la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="Opzioni di registrazione delle origini" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)