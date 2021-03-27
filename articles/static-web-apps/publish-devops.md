---
title: 'Esercitazione: pubblicare app Web statiche di Azure con Azure DevOps'
description: Informazioni su come usare Azure DevOps per pubblicare app Web statiche di Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 701f999427d743c18f5dbcadb00cf303f97a8f53
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627290"
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

1. Selezionare **Importa** per avviare l'importazione di un'applicazione di esempio.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Repository DevOps":::

1. In **Clone URL** immettere `https://github.com/staticwebdev/vanilla-api.git` .

1. Selezionare **Importa**.

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

1. Una volta completata la distribuzione, passare alla nuova risorsa app Web statiche.

1. Selezionare **Gestisci token di distribuzione**.

1. Copiare il **token di distribuzione** e incollarlo in un editor di testo per usarlo in un'altra schermata.

    > [!NOTE]
    > Questo valore è accantonato per il momento perché verranno copiati e incollati altri valori nei passaggi successivi.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token di distribuzione":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Creare l'attività pipeline in Azure DevOps

1. Passare al repository Azure DevOps creato in precedenza.

1. Selezionare **Configura compilazione**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline di compilazione":::

1. Nella schermata *Configura pipeline* selezionare **Starter pipeline**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Configura pipeline":::

1. Copiare e incollare il codice YAML seguente nella pipeline.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Se non si usa l'app di esempio, i valori per `app_location` , `api_location` e `output_location` devono essere modificati in modo da corrispondere ai valori nell'applicazione.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Il `azure_static_web_apps_api_token` valore è gestito autonomamente e configurato manualmente.

1. Selezionare le **variabili**.

1. Consente di creare una nuova variabile.

1. Assegnare un nome alla variabile **deployment_token** (che corrisponde al nome nel flusso di lavoro).

1. Copiare il token di distribuzione incollato in precedenza in un editor di testo.

1. Incollare il token di distribuzione nella casella _valore_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token variabile":::

1. Selezionare **Mantieni segreto valore**.

1. Selezionare **OK**.

1. Selezionare **Save (Salva** ) per tornare alla pipeline YAML.

1. Selezionare **Save ed Run** per aprire la finestra di dialogo _Salva ed Esegui_ .

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

1. Selezionare **Save ed Run** per eseguire la pipeline.

1. Una volta completata la distribuzione, passare alla **Panoramica** delle app Web statiche di Azure, che include collegamenti alla configurazione della distribuzione. Si noti che il collegamento di _origine_ ora punta al ramo e alla località del repository Azure DevOps.

1. Selezionare l' **URL** per visualizzare il sito Web appena distribuito.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Percorso di distribuzione":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare app Web statiche di Azure](./configuration.md)
