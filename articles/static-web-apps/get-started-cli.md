---
title: "Avvio rapido: Creazione del primo sito statico con App Web statiche di Azure usando l'interfaccia della riga di comando"
description: Informazioni su come distribuire un sito statico in App Web statiche di Azure con l'interfaccia della riga di comando di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: a95e1658c3633f4ae8d09b71e9d3b0c82446754a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727587"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Avvio rapido: Creazione del primo sito statico con l'interfaccia della riga di comando di Azure

App Web statiche di Azure consente di pubblicare un sito Web in un ambiente di produzione creando app da un repository GitHub. In questa guida di avvio rapido si distribuisce un'applicazione Web in App Web statiche di Azure usando l'interfaccia della riga di comando di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

- [GitHub](https://github.com)
- [Token di accesso personale di GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- Account [Azure](https://portal.azure.com)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) installata (versione 2.8.0 e successive)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Passare quindi alla nuova cartella usando il comando seguente.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

Dopo aver creato il repository, è possibile creare un'app Web statica dall'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> Assicurarsi di trovarsi nella cartella _my-first-static-web-app_ nel terminale.

1. Accedere all'interfaccia della riga di comando di Azure usando il comando seguente.

    ```azurecli
    az login
    ```

1. Creare una nuova app Web statica dal repository.

    # <a name="no-framework"></a>[Nessun framework](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---
    
    > [!IMPORTANT]
    > L'URL passato al `s` parametro non deve includere il `.git` suffisso.

    - `<RESOURCE_GROUP_NAME>`: Sostituire questo valore con un [nome del gruppo di risorse di Azure](../azure-resource-manager/management/manage-resources-cli.md)esistente.

      - Per informazioni dettagliate sull'elenco dei gruppi di risorse, vedere la documentazione [AZ Group](/cli/azure/group#az_group_list) .

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Sostituire questo valore con il nome utente GitHub.

    - `<LOCATION>`: Sostituire questo valore con la località più vicina. Le opzioni includono: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ e _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Sostituire questo valore con il [token di accesso personale GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) generato in precedenza.

    È ora possibile visualizzare l'app creata in Azure.

1. Aprire il [portale di Azure](https://portal.azure.com).

1. Cercare **my-first-web-static-app** nella barra di ricerca in alto.

1. Selezionare **my-first-web-static-app**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Pulizia delle risorse

Se non si intende continuare a usare questa applicazione, è possibile eliminare l'istanza di App Web statiche di Azure eseguendo questo comando:

```azurecli
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)