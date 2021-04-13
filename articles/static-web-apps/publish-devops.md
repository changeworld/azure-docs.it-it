---
title: 'Esercitazione: Pubblicare App Web statiche di Azure con Azure DevOps'
description: Informazioni su come usare Azure DevOps per pubblicare App Web statiche di Azure.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: f82ae60ab7f57b20a727deefa6e286d698ee5b6c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365757"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Esercitazione: Pubblicare App Web statiche di Azure con Azure DevOps

Questo articolo illustra come distribuire [in](./overview.md) App Web statiche di Azure usando [Azure DevOps](https://dev.azure.com/).

In questa esercitazione si apprenderà come:

- Configurare un sito App Web statiche di Azure locale
- Creare una Azure DevOps pipeline per compilare e pubblicare un'app Web statica

## <a name="prerequisites"></a>Prerequisiti

- **Account Azure attivo:** Se non è disponibile, è possibile [creare un account gratuitamente.](https://azure.microsoft.com/free/)
- **Azure DevOps progetto:** Se non è disponibile, è possibile [creare un progetto gratuitamente.](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)
- **Azure DevOps Pipeline:** Per informazioni introduttive, vedere [Creare la prima pipeline.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true)

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Creare un'app Web statica in un repository Azure DevOps web

  > [!NOTE]
  > Se nel repository è presente un'app esistente, è possibile passare alla sezione successiva.

1. Passare al repository Azure DevOps dati.

1. Selezionare **Importa per** iniziare a importare un'applicazione di esempio.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps Repo":::

1. In **CLONE URL (URL clone)** immettere `https://github.com/staticwebdev/vanilla-api.git` .

1. Selezionare **Importa**.

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

1. Passare al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**.

1. Cercare **App Web statiche**.

1. Selezionare **App Web statiche (anteprima)**.

1. Selezionare **Crea**.

1. In _Dettagli distribuzione_ assicurarsi di selezionare **Altro**. In questo modo è possibile usare il codice nel repository Azure DevOps dati.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Dettagli della distribuzione - altro":::

1. Al termine della distribuzione, passare alla nuova risorsa App Web statiche.

1. Selezionare **Gestisci token di distribuzione.**

1. Copiare **il token di distribuzione** e incollarlo in un editor di testo per usarlo in un'altra schermata.

    > [!NOTE]
    > Questo valore viene accantonato per il momento perché nei passaggi successivi verranno copiati e incollati altri valori.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token di distribuzione":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Creare l'attività pipeline in Azure DevOps

1. Passare al repository Azure DevOps creato in precedenza.

1. Selezionare **Configura compilazione.**

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Pipeline di compilazione":::

1. Nella schermata *Configura la pipeline* selezionare Pipeline **iniziale.**

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Configurare la pipeline":::

1. Copiare e incollare il codice YAML seguente nella pipeline.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Se non si usa l'app di esempio, è necessario modificare i valori per , e in modo che corrispondano `app_location` `api_location` ai valori `output_location` nell'applicazione.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Il `azure_static_web_apps_api_token` valore è auto-gestito e viene configurato manualmente.

1. Selezionare **Variabili**.

1. Consente di creare una nuova variabile.

1. Assegnare alla variabile **deployment_token** (corrispondente al nome nel flusso di lavoro).

1. Copiare il token di distribuzione incollato in precedenza in un editor di testo.

1. Incollare il token di distribuzione nella _casella_ Valore.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token variabile":::

1. Selezionare **Mantieni questo valore segreto.**

1. Selezionare **OK**.

1. Selezionare **Salva** per tornare alla pipeline YAML.

1. Selezionare **Salva ed esegui** per aprire la finestra di dialogo Salva _ed_ esegui.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

1. Selezionare **Salva ed esegui** per eseguire la pipeline.

1. Al termine della distribuzione, passare alla App Web statiche di Azure **panoramica,** che include collegamenti alla configurazione della distribuzione. Si noti che _il collegamento_ Origine ora punta al ramo e al percorso del repository Azure DevOps origine.

1. Selezionare **l'URL** per visualizzare il sito Web appena distribuito.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Percorso di distribuzione":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare App Web statiche di Azure](./configuration.md)
