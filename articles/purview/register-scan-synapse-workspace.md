---
title: Come analizzare le aree di lavoro di Azure sinapsi
description: Informazioni su come eseguire la scansione di un'area di lavoro sinapsi nel Catalogo dati di Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031406"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Registrare e analizzare le aree di lavoro di Azure sinapsi

Questo articolo illustra come registrare un'area di lavoro di Azure sinapsi in ambito e configurare un'analisi su di essa.

## <a name="supported-capabilities"></a>Funzionalità supportate

Le analisi dell'area di lavoro di Azure sinapsi supportano l'acquisizione di metadati e schema per database SQL dedicati e senza server al loro interno. Inoltre, i dati vengono classificati automaticamente in base alle regole di classificazione personalizzate e di sistema.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure
- Configurazione dell'autenticazione come descritto nelle sezioni seguenti

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Impostazione dell'autenticazione per l'enumerazione delle risorse del database SQL dedicato in un'area di lavoro sinapsi

1. Passare al **gruppo di risorse** o alla **sottoscrizione** in cui si trova l'area di lavoro sinapsi, nel portale di Azure.  
1. Selezionare **controllo di accesso (IAM)**   dal menu di spostamento a sinistra 
1. Per aggiungere un ruolo al gruppo di risorse o alla sottoscrizione, è necessario essere un proprietario o un amministratore accesso utenti. Selezionare *+ Aggiungi* pulsante. 
1. Impostare il ruolo **lettore** e immettere il nome dell'account Azure (che rappresenta il relativo MSI) in Seleziona casella di input. Fare clic su *Salva* per completare l'assegnazione di ruolo.
1. Seguire i passaggi da 2 a 4 sopra per aggiungere anche il ruolo **lettore dati BLOB di archiviazione** per l'identità del servizio gestito di Azure per il gruppo di risorse o la sottoscrizione in cui si trova l'area di lavoro sinapsi.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Impostazione dell'autenticazione per l'enumerazione delle risorse del database SQL senza server in un'area di lavoro sinapsi

> [!NOTE]
> Per eseguire questi comandi, è necessario essere un **amministratore di sinapsi** nell'area di lavoro. Altre informazioni sulle autorizzazioni sinapsi sono disponibili [qui](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Passare all'area di lavoro sinapsi
1. Passare alla sezione **dati** e a uno dei database SQL senza server
1. Fare clic sull'icona dei puntini di sospensione e avviare un nuovo script SQL
1. Aggiungere l'identità del servizio gestito di Azure (rappresentata dal nome account) come **sysadmin** nei database SQL senza server eseguendo il comando seguente nello script SQL:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Impostazione dell'autenticazione per l'analisi delle risorse in un'area di lavoro sinapsi

Esistono tre modi per configurare l'autenticazione per un'origine sinapsi di Azure:

- Identità gestita
- Entità servizio
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Uso dell'identità gestita per database SQL dedicati

1. Passare all' **area di lavoro sinapsi**
1. Passare alla sezione **dati** e a uno dei database SQL senza server
1. Fare clic sull'icona dei puntini di sospensione e avviare un nuovo script SQL
1. Aggiungere l'identità del servizio gestito di Azure, rappresentata dal nome dell'account, come **db_owner** nel database SQL dedicato eseguendo il comando seguente nello script SQL:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Uso dell'identità gestita per database SQL senza server

1. Passare all' **area di lavoro sinapsi**
1. Passare alla sezione **dati** e a uno dei database SQL senza server
1. Fare clic sull'icona dei puntini di sospensione e avviare un nuovo script SQL
1. Aggiungere l'identità del servizio gestito di Azure (rappresentata dal nome account) come **sysadmin** nei database SQL senza server eseguendo il comando seguente nello script SQL:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Uso dell'entità servizio per database SQL dedicati

> [!NOTE]
> Per prima cosa, è necessario configurare una nuova **credenziale** di tipo entità servizio seguendo le istruzioni riportate [qui](manage-credentials.md).

1. Passare all' **area di lavoro sinapsi**
1. Passare alla sezione **dati** e a uno dei database SQL senza server
1. Fare clic sull'icona dei puntini di sospensione e avviare un nuovo script SQL
1. Aggiungere l' **ID dell'entità servizio** come **db_owner** nel database SQL dedicato eseguendo il comando seguente nello script SQL:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Uso dell'entità servizio per database SQL senza server

1. Passare all' **area di lavoro sinapsi**
1. Passare alla sezione **dati** e a uno dei database SQL senza server
1. Fare clic sull'icona dei puntini di sospensione e avviare un nuovo script SQL
1. Aggiungere l'identità del servizio gestito di Azure (rappresentata dal nome account) come **sysadmin** nei database SQL senza server eseguendo il comando seguente nello script SQL:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> È necessario configurare l'autenticazione in ogni database SQL dedicato all'interno dell'area di lavoro sinapsi, che si intende registrare e analizzare. Le autorizzazioni indicate in precedenza per il database SQL senza server si applicano a tutte all'interno dell'area di lavoro, ad esempio, sarà necessario eseguirle una sola volta.
    
## <a name="register-an-azure-synapse-source"></a>Registrare un'origine di sinapsi di Azure

Per registrare una nuova origine della sinapsi di Azure nel Catalogo dati, seguire questa procedura:

1. Passare all'account Purview
1. Selezionare **Origini** nel riquadro di spostamento sinistro
1. Selezionare **Registra**
1. In **registra origini** selezionare **Azure sinapsi Analytics (multiple)**
1. Selezionare **Continua**

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Configurare l'origine sinapsi di Azure":::

Nella schermata **Register Sources (Azure sinapsi Analytics)** eseguire le operazioni seguenti:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
1. Facoltativamente, scegliere una **sottoscrizione** a cui applicare il filtro.
1. **Selezionare un nome dell'area di lavoro sinapsi** nell'elenco a discesa. Gli endpoint SQL vengono compilati automaticamente in base alla selezione dell'area di lavoro. 
1. Selezionare una **raccolta** o crearne una nuova (facoltativo)
1. **Fine** per registrare l'origine dati

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Dettagli di riempimento per l'origine della sinapsi di Azure":::

## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

Per creare ed eseguire una nuova analisi, procedere come segue:

1. Passare alla sezione **Sources** .

1. Selezionare l'origine dati registrata.

1. Fare clic su Visualizza dettagli e selezionare **+ nuova analisi** oppure usare l'icona di azione rapida di analisi nel riquadro di origine

1. Immettere il *nome* e selezionare tutti i tipi di risorsa che si vuole analizzare all'interno di questa origine. Il **database SQL** è l'unico tipo supportato attualmente all'interno di un'area di lavoro sinapsi.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Analisi origine sinapsi di Azure":::

1. Selezionare le **credenziali** per la connessione alle risorse all'interno dell'origine dati. 
  
1. All'interno di ogni tipo è possibile scegliere di eseguire l'analisi di tutte le risorse o di un subset di essi in base al nome.
1.  Scegliere **Continua** per procedere. 

1.  Selezionare un **set di regole di analisi** di tipo SQL sinapsi di Azure. È anche possibile creare set di regole di analisi inline.

1. Scegliere il trigger dell'analisi. È possibile pianificarne l'esecuzione **ogni settimana/mese** o **una volta**

1. Esaminare l'analisi e selezionare Salva per completare la configurazione   

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizzazione delle analisi e delle relative esecuzioni

1. Per visualizzare i dettagli dell'origine, fare clic su **Visualizza dettagli** nel riquadro nella sezione origini. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Dettagli origine della sinapsi di Azure"::: 

1. Visualizzare i dettagli dell'esecuzione dell'analisi passando alla pagina dei **Dettagli dell'analisi** .
    1. La *barra di stato* è un breve riepilogo sullo stato di esecuzione delle risorse figlio. Verrà visualizzato nell'analisi a livello di area di lavoro.
    1. Verde significa che ha avuto esito positivo, mentre quello rosso non è riuscito. Grigio indica che l'analisi è ancora in corso
    1. È possibile fare clic su ogni analisi per visualizzare i dettagli con granularità più fine

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Dettagli analisi sinapsi di Azure" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Visualizzare un riepilogo delle esecuzioni dell'analisi non riuscita recenti nella parte inferiore della pagina dei dettagli dell'origine. È anche possibile fare clic su Visualizza dettagli più granulari relativi a tali esecuzioni.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gestire le analisi: modificare, eliminare o annullare
Per gestire o eliminare un'analisi, procedere come segue:

- Passare al centro di gestione. Selezionare Origini dati nella sezione Sources and scanning (Origini e analisi), quindi selezionare l'origine dati desiderata.

- Selezionare l'analisi da gestire. Per modificare l'analisi, selezionare Modifica.

- Per eliminare l'analisi, selezionare Elimina.
- Se un'analisi è in esecuzione, è possibile annullarla.

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)   