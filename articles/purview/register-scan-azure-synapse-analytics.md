---
title: Come analizzare Azure sinapsi Analytics
description: Questa guida descrive i dettagli su come eseguire l'analisi di Azure sinapsi Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: e0a1d8dba9ea284322584de3b4be2ae390d15fdf
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920251"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Registrare e analizzare analisi sinapsi di Azure

Questo articolo illustra come registrare e analizzare un'istanza di Azure sinapsi Analytics (in precedenza SQL DW) in ambito di competenza.

## <a name="supported-capabilities"></a>Funzionalità supportate

Azure sinapsi Analytics (noto in precedenza come SQL DW) supporta le analisi complete e incrementali per l'acquisizione dei metadati e dello schema. Le analisi classificano anche i dati automaticamente in base alle regole di classificazione personalizzate e di sistema.

### <a name="known-limitations"></a>Limitazioni note

Azure competenza non supporta l'analisi delle [visualizzazioni](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) in Azure sinapsi Analytics

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure
- Accesso alla rete tra l'account di competenza e Azure sinapsi Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Impostazione dell'autenticazione per un'analisi

Esistono tre modi per configurare l'autenticazione per l'archiviazione BLOB di Azure:

- Identità gestita
- Autenticazione SQL
- Entità servizio

    > [!Note]
    > Solo l'account di accesso dell'entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database `loginmanager` nel database master possono creare nuovi account di accesso. Sono necessari circa 15 minuti dopo la concessione dell'autorizzazione. l'account di competenza deve disporre delle autorizzazioni appropriate per poter eseguire l'analisi delle risorse.

### <a name="managed-identity-recommended"></a>Identità gestita (scelta consigliata) 
   
L'account di competenza ha una propria identità gestita, che è fondamentalmente il nome dell'ambito al momento della creazione. È necessario creare un utente Azure AD in Azure sinapsi Analytics (denominato in precedenza SQL DW) con il nome di identità gestita di tale ambito attenendosi ai prerequisiti e all'esercitazione su come [creare Azure ad utenti che usano Azure ad applicazioni](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial).

Esempio di sintassi SQL per creare l'utente e concedere l'autorizzazione:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

L'autenticazione deve disporre dell'autorizzazione per ottenere i metadati per il database, gli schemi e le tabelle. Deve inoltre essere in grado di eseguire una query sulle tabelle per campionare la classificazione. Si consiglia di assegnare `db_owner` l'autorizzazione all'identità.

### <a name="service-principal"></a>Entità servizio

Per utilizzare l'autenticazione basata su entità servizio per le analisi, è possibile utilizzarne una esistente o crearne una nuova. 

> [!Note]
> Se è necessario creare una nuova entità servizio, attenersi alla seguente procedura:
> 1. Passare al [portale di Azure](https://portal.azure.com).
> 1. Selezionare **Azure Active Directory** dal menu a sinistra.
> 1. Selezionare **Registrazioni per l'app**.
> 1. Selezionare **+ Registrazione nuova applicazione**.
> 1. Immettere un nome per l' **applicazione** (il nome dell'entità servizio).
> 1. Selezionare **account solo in questa directory aziendale**.
> 1. Per URI di reindirizzamento selezionare **Web** e immettere l'URL desiderato; non è necessario che sia reale o funzionante.
> 1. Selezionare **Registra**.

È necessario ottenere l'ID applicazione e il segreto dell'entità servizio:

1. Passare all'entità servizio nel [portale di Azure](https://portal.azure.com)
1. Copiare i valori **ID dell'applicazione (client)** da **Panoramica** e **segreto client** da **certificati & segreti**.
1. Passare a Key Vault
1. Selezionare **le impostazioni > segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** della scelta e il **valore** come **segreto client** dall'entità servizio
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso alla propria competenza, sarà necessario [creare una nuova connessione](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) dell'insieme di credenziali delle chiavi
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Concessione dell'accesso dell'entità servizio ad Azure sinapsi Analytics (in precedenza SQL DW)

Inoltre, è necessario creare un Azure AD utente in Azure sinapsi Analytics attenendosi ai prerequisiti e all'esercitazione su come [creare Azure ad utenti che usano Azure ad applicazioni](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial). Esempio di sintassi SQL per creare l'utente e concedere l'autorizzazione:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Per eseguire l'analisi, è necessario che l' **ID applicazione (client)** e il **segreto client** siano necessari per la competenza.

### <a name="sql-authentication"></a>Autenticazione SQL

È possibile seguire le istruzioni in [creare un account di accesso](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) per creare un account di accesso per Azure sinapsi Analytics (in precedenza SQL DW) se non è già presente.

Quando il metodo di autenticazione selezionato è **l'autenticazione SQL**, è necessario ottenere la password e archiviare l'insieme di credenziali delle chiavi:

1. Ottenere la password per l'account di accesso SQL
1. Passare a Key Vault
1. Selezionare **le impostazioni > segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** e il **valore** come *password* per l'account di accesso SQL
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso alla propria competenza, sarà necessario [creare una nuova connessione](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) dell'insieme di credenziali delle chiavi
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando la chiave per configurare l'analisi

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Registrare un'istanza di Azure sinapsi Analytics (in precedenza SQL DW)

Per registrare un nuovo server Azure sinapsi Analytics nella Data Catalog, seguire questa procedura:

1. Passa all'account di competenza
1. Selezionare le **origini** nel percorso di spostamento a sinistra
1. Selezionare **Registra**
1. In **registra origini** selezionare **Azure sinapsi Analytics (in precedenza SQL DW)**
1. Selezionare **Continua**

Nella schermata **Register Sources (Azure sinapsi Analytics)** eseguire le operazioni seguenti:

1. Immettere un **nome** con cui l'origine dati verrà elencata nel catalogo.
1. Scegliere il modo in cui si desidera puntare all'account di archiviazione desiderato:
   1. Selezionare una **sottoscrizione di Azure**, selezionare la sottoscrizione appropriata nella casella di riepilogo a discesa **sottoscrizione di Azure** e il server appropriato nella casella di riepilogo a discesa **nome server** .
   1. In alternativa, è possibile selezionare **invio manualmente** e immettere un **nome di server**.
1. **Completare** la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="opzioni registra origini" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare il Catalogo dati di Azure per le competenze](how-to-browse-catalog.md)
- [Cerca nel Data Catalog di competenza di Azure](how-to-search-catalog.md)

