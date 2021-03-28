---
title: Registrare e analizzare Database SQL di Azure
description: Questa esercitazione descrive come analizzare Database SQL di Azure
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 74a8bc4209696682c294bccab450d25ae86e3645
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643926"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Registrare e analizzare Database SQL di Azure

Questo articolo illustra come registrare un'origine dati Database SQL di Azure in Purview e configurarne un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine dati Database SQL di Azure supporta le funzionalità seguenti:

- **Analisi complete e incrementali** per acquisire metadati e classificazioni in Database SQL di Azure.

- **Derivazione** tra asset di dati per le attività di copia e del flusso di dati di Azure Data Factory.

### <a name="known-limitations"></a>Limitazioni note

Azure Purview non supporta l'analisi di [viste](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) in Database SQL di Azure.

## <a name="prerequisites"></a>Prerequisiti

1. Creare un nuovo account Purview se non se ne ha già uno.

1. Accesso di rete tra l'account Purview e Database SQL di Azure.


### <a name="set-up-authentication-for-a-scan"></a>Configurare l'autenticazione per un'analisi

Configurare l'autenticazione per analizzare Database SQL di Azure. Se è necessario creare una nuova autenticazione, bisogna [autorizzare l'accesso di database a Database SQL](../azure-sql/database/logins-create-manage.md). Attualmente Purview supporta tre metodi di autenticazione:

- Autenticazione SQL
- Entità servizio
- Identità gestita

#### <a name="sql-authentication"></a>Autenticazione SQL

> [!Note]
> Solo l'account di accesso dell'entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database `loginmanager` nel database master possono creare nuovi account di accesso. Circa **15 minuti** dopo aver concesso l'autorizzazione, l'account Purview dovrebbe avere le autorizzazioni appropriate per analizzare le risorse.

È possibile seguire le istruzioni riportate nell'articolo [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) per creare un account di accesso per Database SQL di Azure, se non è disponibile. Per i passaggi successivi, sarà necessario specificare **nome utente** e **password**.

1. Passare all'insieme di credenziali delle chiavi nel portale di Azure
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ Genera/Importa** e immettere il **Nome** e il **Valore** come *password* di Database SQL di Azure
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando il **nome utente** e la **password** per configurare l'analisi

#### <a name="service-principal-and-managed-identity"></a>Entità servizio e identità gestita

Per consentire a Purview di usare l'entità servizio o l'**identità gestita** di Purview per analizzare Database SQL di Azure, è necessario completare diversi passaggi

   > [!Note]
   > Purview richiede l'**ID applicazione (client)** e il **segreto client** per eseguire l'analisi.

##### <a name="create-or-use-an-existing-service-principal"></a>Creare un'entità servizio o usarne una esistente

> [!Note]
> Ignorare questo passaggio se si usa l'**identità gestita** di Purview

È possibile usare un'entità servizio esistente o crearne una nuova. 

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

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configurare l'autenticazione di Azure AD nell'account del database

L'entità servizio o l'identità gestita deve avere l'autorizzazione per ottenere metadati per il database, gli schemi e le tabelle. Deve inoltre essere in grado di eseguire query sulle tabelle per campionare la classificazione.

- [Configurare e gestire l'autenticazione di Azure AD con Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Se si usa un'identità gestita, l'account Purview include la propria identità gestita, che è essenzialmente il nome di Purview specificato durante la creazione. È necessario creare un utente di Azure AD in Database SQL di Azure con l'identità gestita di Purview o la propria entità servizio seguendo l'esercitazione [Creare l'utente dell'entità servizio in Database SQL di Azure](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). È necessario assegnare l'autorizzazione corretta (ad esempio `db_owner` o `db_datareader`) all'identità. Esempio di sintassi SQL per creare l'utente e concedere l'autorizzazione:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username` è l'entità servizio dell'utente o l'identità gestita di Purview. Vedere altre informazioni sui [ruoli predefiniti del database e le relative funzionalità](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Aggiungere l'entità servizio all'insieme di credenziali delle chiavi e alle credenziali di Purview

> [!Note]
> Se si prevede di usare l'**identità gestita** di Purview, è possibile ignorare questo passaggio, perché l'identità gestita di Purview è già presente nelle credenziali **Purview-MSI**.

È necessario ottenere l'ID applicazione e il segreto dell'entità servizio:

1. Passare all'entità servizio nel [portale di Azure](https://portal.azure.com)
1. Copiare il valore di **ID applicazione (client)** da **Panoramica** e quello di **Segreto client** da **Certificati e segreti**.
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ Genera/Importa** e immettere il **Nome** scelto e il **Valore** come **Segreto client** dell'entità servizio
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi

### <a name="firewall-settings"></a>Impostazioni del firewall

Il server di database deve consentire l'abilitazione delle connessioni di Azure. In questo modo Azure Purview potrà raggiungere il server e connettersi. È possibile seguire la procedura [Connessioni dall'interno di Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure).

1. Passare all'account di database
1. Selezionare il nome del server nella pagina **Panoramica**
1. Selezionare **Sicurezza > Firewall e reti virtuali**
1. Selezionare **Sì** per **Consenti alle risorse e ai servizi di Azure di accedere a questo server**

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Consenti alle risorse e ai servizi di Azure di accedere a questo server." border="true":::
    
> [!Note]
> Attualmente Azure Purview non supporta la configurazione di rete virtuale. Pertanto, non è possibile specificare impostazioni del firewall basate su IP.

## <a name="register-an-azure-sql-database-data-source"></a>Registrare un'origine dati di Database SQL di Azure

Per registrare una nuova istanza di Database SQL di Azure nel catalogo, procedure come segue:

1. Passare all'account Purview

1. Selezionare **Origini** nel riquadro di spostamento sinistro

1. Selezionare **Registra**

1. In **Register sources** (Registra origini) selezionare **Database SQL di Azure**. Selezionare **Continua**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="Registrare una nuova origine dati" border="true":::

Nella schermata **Register sources (Azure SQL Database)** (Registra origini - Database SQL di Azure) seguire questa procedura:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
1. Selezionare **Dalla sottoscrizione di Azure**, quindi selezionare la sottoscrizione appropriata nell'elenco a discesa **Sottoscrizione di Azure** e il server appropriato nell'elenco a discesa **Nome del server**.
1. Selezionare **Fine** per completare la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="Opzioni di registrazione delle origini" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * L'eliminazione dell'analisi non rimuove gli asset dalle precedenti analisi di Database SQL di Azure.
> * L'asset non verrà più aggiornato con le modifiche dello schema se la tabella di origine viene modificata e si ripete l'analisi della tabella di origine dopo aver modificato la descrizione nella scheda Schema di competenza.

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)
