---
title: 'Avvio rapido: Connettersi ad Azure PostgreSQL con GitHub Actions'
description: Usare Azure PostgreSQL da un flusso di lavoro GitHub Actions
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 2e546801f95d9d884bdfb3f09a18b3fa6e2d78a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364987"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Avvio rapido: Usare GitHub Actions per connettersi ad Azure PostgreSQL

<Token>**SI APPLICA A:** :::image type="icon" source="./media/applies-to/yes.png" border="false":::Database di Azure per PostgreSQL - Server singolo :::image type="icon" source="./media/applies-to/yes.png" border="false":::Database di Azure per PostgreSQL - Server flessibile</Token>

Usare un flusso di lavoro [GitHub Actions](https://docs.github.com/en/actions) per distribuire aggiornamenti di database a [Database di Azure per PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Prerequisiti

Prerequisiti:
- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un repository GitHub con i dati di esempio (`data.sql`). Se non si ha un account GitHub, è possibile [iscriversi gratuitamente](https://github.com/join).
- Un server di Database di Azure per PostgreSQL.
    - [Guida introduttiva: Creare un database di Azure per il server PostgreSQL nel portale di Azure](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Panoramica dei file del flusso di lavoro

Un flusso di lavoro GitHub Actions viene definito da un file YAML (con estensione yml) nel percorso `/.github/workflows/` del repository. Questa definizione contiene i vari passaggi e i parametri che costituiscono il flusso di lavoro.

Il file è costituito da due sezioni:

|Sezione  |Attività  |
|---------|---------|
|**autenticazione** | 1. Definire un'entità servizio. <br /> 2. Creare un segreto GitHub. |
|**Distribuzione** | 1. Distribuire il database. |

## <a name="generate-deployment-credentials"></a>Generare le credenziali per la distribuzione

È possibile creare un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) con il comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) dell'[interfaccia della riga di comando di Azure](/cli/azure/). Eseguire questo comando con [Azure Cloud Shell](https://shell.azure.com/) nel portale di Azure oppure selezionando il pulsante **Prova**.

Sostituire il segnaposto `server-name` con il nome del server PostgreSQL ospitato in Azure. Sostituire `subscription-id` e `resource-group` con l'ID sottoscrizione e il gruppo di risorse connessi al server PostgreSQL.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

L'output è un oggetto JSON con le credenziali di assegnazione di ruolo che forniscono l'accesso al database in modo simile a questo esempio. Copiare l'oggetto JSON di output per un uso successivo.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> È sempre consigliabile concedere l'accesso minimo. L'ambito dell'esempio precedente è limitato al server specifico e non include l'intero gruppo di risorse.

## <a name="copy-the-postgresql-connection-string"></a>Copiare la stringa di connessione PostgreSQL

Nel portale di Azure passare a Database di Azure per PostgreSQL e aprire **Impostazioni** > **Stringhe di connessione**. Copiare la stringa di connessione per **ADO.NET**. Sostituire i valori segnaposto `your_database` e `your_password`. La stringa di connessione avrà un aspetto simile a questo.

> [!IMPORTANT]
> - Per Server singolo usare ```user=adminusername@servername```. Si noti che ```@servername``` è obbligatorio.
> - Per Server flessibile usare ```user= adminusername``` senza ```@servername```.

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
```

La stringa di connessione verrà usata come segreto GitHub.

## <a name="configure-the-github-secrets"></a>Configurare i segreti GitHub

1. In [GitHub](https://github.com/) esplorare il repository.

1. Selezionare **Settings > Secrets > New secret** (Impostazioni > Segreti > Nuovo segreto).

1. Incollare l'intero output JSON del comando dell'interfaccia della riga di comando di Azure nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_CREDENTIALS`.

    Quando in seguito si configura il file del flusso di lavoro, si usa il segreto come `creds` di input dell'azione di accesso di Azure. Ad esempio:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Selezionare di nuovo **New secret** (Nuovo segreto).

1. Incollare il valore della stringa di connessione nel campo del valore del segreto. Assegnare al segreto il nome `AZURE_POSTGRESQL_CONNECTION_STRING`.


## <a name="add-your-workflow"></a>Aggiungere il flusso di lavoro

1. Passare ad **Actions** per il repository GitHub.

2. Selezionare **Set up your workflow yourself** (Configurare manualmente il flusso di lavoro).

2. Eliminare tutti quello che segue la sezione `on:` del file del flusso di lavoro. Il flusso di lavoro rimanente, ad esempio, potrà essere simile al seguente.

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Rinominare il flusso di lavoro `PostgreSQL for GitHub Actions` e aggiungere le azioni checkout e login. Queste azioni eseguiranno il checkout del codice del sito e l'autenticazione con Azure usando il segreto GitHub `AZURE_CREDENTIALS` creato in precedenza.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Usare l'azione di distribuzione di Azure PostgreSQL per connettersi all'istanza di PostgreSQL. Sostituire `POSTGRESQL_SERVER_NAME` con il nome del server. È necessario avere un file di dati PostgreSQL denominato `data.sql` al livello radice del repository.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Completare il flusso di lavoro aggiungendo un'azione di disconnessione da Azure. Ecco il flusso di lavoro completato. Il file verrà visualizzato nella cartella `.github/workflows` del repository.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Esaminare la distribuzione

1. Passare ad **Actions** per il repository GitHub.

1. Aprire il primo risultato per visualizzare i log dettagliati dell'esecuzione del flusso di lavoro.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Log di esecuzione di GitHub Actions":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il database PostgreSQL di Azure e il repository non sono più necessari, pulire le risorse distribuite eliminando il gruppo di risorse e il repository GitHub.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'integrazione di Azure e GitHub](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Informazioni su come connettersi al server](how-to-connect-query-guide.md)
