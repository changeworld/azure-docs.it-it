---
title: Reimpostare i token di distribuzione nelle app Web statiche di Azure (anteprima)
description: Reimpostare i token in un sito di app Web statiche di Azure
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745539"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Reimpostare i token di distribuzione nelle app Web statiche di Azure (anteprima)

Quando si crea un nuovo sito di app Web statiche di Azure, Azure genera un token usato per identificare l'applicazione durante la distribuzione. Durante il provisioning, questo token viene archiviato come segreto nel repository GitHub. Questo articolo illustra come usare e gestire questo token.

In genere, non è necessario preoccuparsi del token di distribuzione, ma di seguito sono riportati alcuni motivi per cui potrebbe essere necessario recuperare o reimpostare il token.

* **Compromissione del token**: reimpostare il token se è esposto a un'entità esterna.
* **Distribuzione da un repository GitHub separato**: se si esegue la distribuzione manuale da un repository GitHub separato, è necessario impostare il token di distribuzione nel nuovo repository.

## <a name="prerequisites"></a>Prerequisiti

- Repository GitHub esistente configurato con App Web statiche di Azure.
- Se non è ancora stato creato, vedere [Compilazione della prima app statica](getting-started.md).

## <a name="reset-a-deployment-token"></a>Reimpostare un token di distribuzione

1. Fare clic sul collegamento **Gestisci token di distribuzione** nella pagina _Panoramica_ del sito app Web statiche di Azure.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Gestione del token di distribuzione":::

1. Fare clic sul pulsante **Reimposta token** .

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Reimpostazione del token di distribuzione":::

1. Dopo aver visualizzato un nuovo token nel campo _token di distribuzione_ , copiare il token facendo clic sull'icona **copia negli Appunti** .


## <a name="update-a-secret-in-the-github-repository"></a>Aggiornare un segreto nel repository GitHub

Per eseguire la distribuzione automatizzata, dopo aver reimpostato un token è necessario impostare il nuovo valore nel repository GitHub corrispondente.

1. Passare al repository del progetto in GitHub e fare clic sulla scheda **Settings (impostazioni** ).
1. Fare clic sulla voce di menu **Secrets** . Si troverà un segreto generato durante il provisioning di app Web statiche denominato _AZURE_STATIC_WEB_APPS_API_TOKEN_... nella sezione _segreti del repository_ .

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Elenco dei segreti del repository":::

    > [!NOTE]
    > Se il sito app Web statiche di Azure è stato creato su più rami di questo repository, vengono visualizzate più _AZURE_STATIC_WEB_APPS_API_TOKEN_... segreti in questo elenco. Selezionare quella corretta associando il nome del file elencato nel campo _Modifica flusso di lavoro_ nella scheda _Panoramica_ del sito app Web statiche.

1. Fare clic sul pulsante **Aggiorna** per aprire l'editor.
1. **Incollare il valore** del token di distribuzione nel campo _valore_ .
1. Fare clic su **Aggiorna segreto**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Aggiornamento del segreto del repository":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Pubblicare da un generatore di siti statici](publish-gatsby.md)
