---
title: 'Esercitazione: pubblicare app Web statiche di Azure con Azure DevOps'
description: Informazioni su come usare Azure DevOps per pubblicare app Web statiche di Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: af359734ff5bfe90dedbb7f8389aecdc6e056654
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543560"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Esercitazione: pubblicare app Web statiche di Azure con Azure DevOps

Questo articolo illustra come eseguire la distribuzione in [app Web statiche di Azure](./overview.md) usando [Azure DevOps](https://dev.azure.com/).

In questa esercitazione si apprenderà come:

- Configurare un sito di app Web statiche di Azure
- Creare una pipeline di Azure DevOps per creare e pubblicare un'app Web statica

## <a name="prerequisites"></a>Prerequisiti

- **Account Azure attivo:** Se non si dispone di un account, è possibile [crearne uno gratuitamente](https://azure.microsoft.com/free/).
- **Progetto DevOps di Azure:** Se non si dispone di un progetto, è possibile [creare gratuitamente un progetto](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Pipeline di Azure DevOps:** Per informazioni introduttive, vedere [creare la prima pipeline](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Creare un'app Web statica in un repository Azure DevOps

  > [!NOTE]
  > Se nel repository è presente un'app esistente, è possibile passare alla sezione successiva.

1. Passare al repository di Azure DevOps.

1. Usare un repository esistente o _importare un repository_ come illustrato di seguito.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Repository DevOps":::

1. Creare un nuovo file per l'app Web front-end.

1. Copiare e incollare il markup HTML seguente nel nuovo file:

    ```html
    <!DOCTYPE html>
    <html lang="en">
  
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" href="styles.css">
      <title>Hello World!</title>
    </head>
  
    <body>
      <main>
        <h1>Hello World!</h1>
      </main>
    </body>
  
    </html>
    ```

1. Salvare il file.

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

1. Passare al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**.

1. Cercare **App Web statiche**.

1. Selezionare **app Web statiche (anteprima)**.

1. Selezionare **Crea**.

1. In _Dettagli distribuzione_ assicurarsi di selezionare **altro**. In questo modo è possibile usare il codice nel repository di Azure DevOps.

    > [!NOTE]
    > La funzionalità di selezione di _altri_ è attualmente in fase di implementazione e potrebbe non essere ancora disponibile in tutte le sottoscrizioni di Azure.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Dettagli distribuzione-altro":::

1. Una volta completata la distribuzione, selezionare **Gestisci token di distribuzione**.

1. Copiare il **token di distribuzione** e incollarlo in un editor di testo per usarlo in un'altra schermata.

    > [!NOTE]
    > Questo valore è accantonato per il momento perché verranno copiati e incollati altri valori nei passaggi successivi.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token di distribuzione":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Creare l'attività pipeline in Azure DevOps

1. Passare al progetto Azure DevOps creato in precedenza.

2. Creare una nuova **pipeline di compilazione** e selezionare **Configura compilazione**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline di compilazione":::

3. Copiare e incollare il codice YAML seguente nella pipeline.

    > [!NOTE]
    > I valori immessi per _app_location_,_api_location_ e _output_location_ dovranno essere modificati per l'app.  

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: frontend 
          api_location: api
          output_location: build
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    Configurare gli input dell'app Web statica di Azure in base alla struttura di cartelle dell'applicazione.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Il `azure_static_web_apps_api_token` valore è gestito autonomamente e configurato manualmente.

4. Selezionare le **variabili**.

5. Consente di creare una nuova variabile.

6. Assegnare un nome alla variabile **deployment_token** (che corrisponde al nome nel flusso di lavoro).

7. Copiare il token di distribuzione incollato in precedenza in un editor di testo.

8. Incollare il token di distribuzione nella casella _valore_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token variabile":::

9. Selezionare **OK**.

10. Selezionare **Save (Salva) ed eseguire** la pipeline.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

11. Una volta completata la distribuzione, passare alla **Panoramica** delle app Web statiche di Azure, che include collegamenti alla configurazione della distribuzione.

12. Selezionare l' **URL** per visualizzare il sito Web appena distribuito. Si noti che il collegamento di _origine_ ora punta al ramo e alla località del repository Azure DevOps.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Percorso di distribuzione":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare app Web statiche di Azure](./configuration.md)
