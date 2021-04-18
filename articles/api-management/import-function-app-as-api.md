---
title: Importare un'app per le funzioni di Azure come API in Gestione API
titleSuffix: Azure API Management
description: Questo articolo illustra come importare un'app per le funzioni di Azure in Gestione API di Azure come API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599289"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importare un'app per le funzioni di Azure come API in Gestione API di Azure

Gestione API di Azure supporta l'importazione di app per le funzioni di Azure come nuove API o aggiungendole ad API esistenti. Il processo genera automaticamente una chiave host nell'app per le funzioni di Azure, a cui viene quindi assegnato un valore denominato in Gestione API di Azure.

Questo articolo illustra come importare e testare un'app per le funzioni di Azure come API in Azure API Management. 

Si apprenderà come:

> [!div class="checklist"]
> * Importare un'app per le funzioni di Azure come API
> * Aggiungere un'app per le funzioni di Azure a un'API
> * Visualizzare la chiave host e il valore denominato di Gestione API di Azure della nuova app per le funzioni di Azure
> * Testare l'API nel portale di Azure

## <a name="prerequisites"></a>Prerequisiti

* Completare la guida introduttiva Creare [un'istanza API Management Azure.](get-started-create-service-instance.md)
* Verificare che nella sottoscrizione sia disponibile un'app Funzioni di Azure. Per altre informazioni, vedere [come creare un'app per le funzioni di Azure](../azure-functions/functions-get-started.md). Le funzioni devono avere il livello di autorizzazione e trigger HTTP impostato su *Anonimo* o *Funzione*.

> [!NOTE]
> È possibile usare l'estensione API Management per Visual Studio Code importare e gestire le API. Seguire [l'API Management di estensione per](visual-studio-code-tutorial.md) installare e iniziare.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importare un'app per le funzioni di Azure come nuova API

Per creare una nuova API da un'app per le funzioni di Azure, seguire questa procedura.

1. Passare al servizio Gestione API nel portale di Azure e selezionare **API** dal menu.

2. Nell'elenco **Add a new API** (Aggiungere una nuova API) selezionare **App per le funzioni**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Screenshot che mostra il riquadro App per le funzioni.":::

3. Fare clic su **Sfoglia** per selezionare le funzioni da importare.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Screenshot con il pulsante Salva evidenziato.":::

4. Fare clic sulla sezione **App per le funzioni** per scegliere dall'elenco di App per le funzioni disponibili.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Screenshot con la sezione App per le funzioni evidenziata.":::

5. Individuare l'app per le funzioni da cui si desidera importare funzioni, selezionarla e fare clic su **Seleziona**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Screenshot con la sezione App per le funzioni evidenziata da cui importare funzioni e il pulsante Seleziona.":::

6. Selezionare le funzioni che si desidera importare e fare clic su **Seleziona**.
    * È possibile importare funzioni solo in base a trigger HTTP con livelli di *autorizzazione* *Anonimo* o Funzione.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Screenshot con le funzioni evidenziate da importare e il pulsante Seleziona.":::

7. Passare alla visualizzazione **Completa** e assegnare **Prodotto** alla nuova API. 
8. Se necessario, specificare altri campi durante la creazione o configurarli in un secondo momento tramite la **scheda** Impostazioni. 
    * Le impostazioni sono illustrate nell'esercitazione [Importare e pubblicare la prima API](import-and-publish.md#import-and-publish-a-backend-api).

    >[!NOTE]
    > I prodotti sono associazioni di una o più API offerte agli sviluppatori tramite il portale per sviluppatori. In primo luogo, gli sviluppatori devono sottoscrivere un prodotto per ottenere l'accesso all'API. Dopo la sottoscrizione, ottengono una chiave di sottoscrizione per qualsiasi API nel prodotto. In qualità di creatore API Management istanza, si è un amministratore e si è sottoscritti a ogni prodotto per impostazione predefinita.
    >
    > Ogni API Management viene fornita con due prodotti di esempio predefiniti:
    > - **Starter**
    > - **Illimitato**

9. Fare clic su **Crea**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a>Aggiungere l'app per le funzioni di Azure a un'API esistente

Per aggiungere l'app per le funzioni di Azure a un'API esistente, seguire questa procedura.

1. Nell'istanza del servizio **Gestione API di Azure** selezionare **API** dal menu a sinistra.

2. Scegliere un'API in cui importare un'app per le funzioni di Azure. Fare clic su **...** e selezionare **Importa** dal menu di scelta rapida.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Screenshot con la voce di menu Importa evidenziata.":::

3. Fare clic sul riquadro **App per le funzioni**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Screenshot con il riquadro App per le funzioni evidenziato.":::

4. Nella finestra popup fare clic su **Sfoglia**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Screenshot del pulsante Sfoglia.":::

5. Fare clic sulla sezione **App per le funzioni** per scegliere dall'elenco di App per le funzioni disponibili.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Screenshot con l'elenco di app per le funzioni evidenziato.":::

6. Individuare l'app per le funzioni da cui si desidera importare funzioni, selezionarla e fare clic su **Seleziona**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Screenshot con la sezione App per le funzioni evidenziata da cui importare funzioni.":::

7. Selezionare le funzioni che si desidera importare e fare clic su **Seleziona**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Screenshot che evidenzia le funzioni da importare.":::

8. Fare clic su **Importa**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Aggiungere da app per le funzioni":::

## <a name="authorization"></a><a name="authorization"></a> Autorizzazione

L'importazione di un'app per le funzioni di Azure genera automaticamente:

* la chiave host nell'app per le funzioni, con il nome apim-{*nome istanza del servizio Gestione API di Azure*},
* il valore denominato nell'istanza di Gestione API di Azure, con il nome {*nome istanza dell'app per le funzioni di Azure*}-key, che contiene la chiave host creata.

Per le API create dopo il 4 aprile 2019, la chiave host viene passata nelle richieste HTTP da Gestione API all'app per le funzioni in un'intestazione. Le API precedenti passano la chiave host come [parametro di query](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). È possibile modificare questo comportamento tramite la `PATCH Backend` [chiamata API REST](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) sull'entità *back-end* associata all'app per le funzioni.

> [!WARNING]
> La rimozione o la modifica del valore della chiave host dell'app per le funzioni di Azure o del valore denominato API Management azure interromperà la comunicazione tra i servizi. I valori non vengono sincronizzati automaticamente.
>
> Se è necessario ruotare la chiave host, assicurarsi che venga modificato anche il valore denominato in Gestione API di Azure.

### <a name="access-azure-function-app-host-key"></a>Accedere alla chiave host dell'app per le funzioni di Azure

1. Passare all'istanza dell'app per le funzioni di Azure.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Screenshot che evidenzia la selezione dell'istanza dell'app per le funzioni.":::

2. Nella sezione **Funzioni** del menu di spostamento laterale selezionare **Chiavi dell'app.**

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Screenshot con l'opzione Impostazioni dell'app per le funzioni evidenziata.":::

3. Trovare le chiavi nella **sezione Chiavi** host.

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Screenshot con la sezione Chiavi host evidenziata.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Accedere al valore denominato in Gestione API di Azure

Passare all'istanza di Gestione API di Azure e selezionare **Valori denominati** nel menu a sinistra. Qui è archiviata la chiave dell'app per le funzioni di Azure.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Aggiungere da app per le funzioni":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Testare la nuova API nel portale di Azure

È possibile chiamare le operazioni direttamente dal portale di Azure. Il portale di Azure offre un sistema pratico per visualizzare e testare le operazioni di un'API.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Screenshot che evidenzia la procedura di test.":::

1. Selezionare l'API creata nella sezione precedente.

2. Selezionare la scheda **Test**.

3. Selezionare l'operazione che si vuole testare.

    * Nella pagina vengono visualizzati i campi per le intestazioni e i parametri di query. 
    * Una delle intestazioni è "Ocp-Apim-Subscription-Key", per la chiave di sottoscrizione del prodotto associata a questa API. 
    * In quanto creatore dell'API Management, l'utente è già un amministratore, quindi la chiave viene compilata automaticamente. 

4. Selezionare **Invia**.

    * Quando il test ha esito positivo, il back-end risponde con **200 OK** e alcuni dati.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)
