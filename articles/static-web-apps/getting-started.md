---
title: 'Avvio rapido: Creazione del primo sito statico con App Web statiche di Azure'
description: Informazioni su come distribuire un sito statico in App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483846"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Avvio rapido: Creazione del primo sito statico con App Web statiche di Azure

App Web statiche di Azure pubblica un sito Web creando app da un repository di codice. In questa guida introduttiva si distribuisce un'applicazione in app Web statiche di Azure usando l Visual Studio Code app.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

- [GitHub](https://github.com)
- Account [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Estensione App Web statiche di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installare Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Aprire quindi Visual Studio Code e passare a **File > Apri** cartella per aprire il repository clonato nel computer nell'editor.

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

1. In Visual Studio Code selezionare il logo di Azure sulla barra delle attività per aprire la finestra delle estensioni di Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo di Azure":::

    > [!NOTE]
    > Sono necessari l'accesso ad Azure e GitHub. Se non è già stato effettuato l'accesso ad Azure e GitHub da Visual Studio Code, l'estensione richiederà di accedere a entrambi durante il processo di creazione.

1. Sotto _l'etichetta App Web statiche_ selezionare il **segno più**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nome applicazione":::

1. Nella parte superiore dell'editor verrà visualizzato il riquadro comandi, in cui verrà richiesto di assegnare un nome all'applicazione.

    Digitare **my-first-static-web-app** e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Creare l'app Web statica":::

1. Selezionare i set di impostazioni corrispondenti al tipo di applicazione.

    # <a name="no-framework"></a>[Nessun framework](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Set di impostazioni dell'applicazione: nessun framework":::

    Immettere **./** come percorso per i file dell'applicazione.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Percorso dei file dell'applicazione":::

    Selezionare **Ignora per il momento** come percorso per l'API Funzioni di Azure.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Posizione dell'API":::

    Immettere **./** come percorso dell'output di compilazione.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Percorso dell'output di compilazione dell'applicazione":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Anche se è presente un set di impostazioni Angular, selezionare **l'opzione Personalizzato** in modo da poter specificare un percorso di output appropriato per questa applicazione.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Set di impostazioni dell'applicazione: Angular":::

    Immettere **./** come percorso per i file dell'applicazione.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Percorso dei file dell'applicazione: Angular":::

    Selezionare **Ignora per il momento** come percorso per l'API Funzioni di Azure.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Percorso API: Angular":::

    Immettere **dist/angular-basic** come percorso dell'output di compilazione.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Percorso dell'output di compilazione dell'applicazione: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Set di impostazioni dell'applicazione: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Set di impostazioni dell'applicazione: Vue":::

    ---

1. Selezionare la località più vicina e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Posizione risorsa":::

1. Al termine della creazione dell'app, in Visual Studio Code verrà visualizzata una notifica di conferma.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Conferma della creazione":::

    Fare quindi clic sul pulsante **Apri azioni in GitHub.** Questa pagina mostra lo stato di compilazione dell'applicazione.

    Al termine dell'azione GitHub, è possibile passare al sito Web pubblicato.

1. Per visualizzare il sito Web nel browser, fare clic con il pulsante destro del mouse sul progetto nell'estensione App Web statiche e scegliere **Sfoglia sito**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Esplorazione del sito":::

## <a name="clean-up-resources"></a>Pulizia delle risorse

Se non si intende continuare a usare questa applicazione, è possibile eliminare l'istanza di App Web statiche di Azure tramite l'estensione.

Nella finestra di esplorazione di Visual Studio Code tornare alla sezione _App Web statiche_, fare clic con il pulsante destro del mouse su **my-first-static-web-app** e quindi scegliere **Elimina**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Eliminare l'app":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)
