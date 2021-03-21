---
title: 'Esercitazione: Creare e pubblicare un prodotto in Gestione API di Azure'
description: Questa esercitazione descrive come creare e pubblicare un prodotto in Gestione API di Azure. Dopo la pubblicazione di un prodotto, gli sviluppatori possono iniziare a usare le API del prodotto.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545157"
---
# <a name="tutorial-create-and-publish-a-product"></a>Esercitazione: Creare e pubblicare un prodotto  

In Gestione API di Azure un [*prodotto*](api-management-terminology.md#term-definitions) contiene una o più API, oltre a una quota di utilizzo e alle condizioni per l'utilizzo. Dopo la pubblicazione di un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a usare le API del prodotto.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare e pubblicare un prodotto
> * Aggiungere un'API al prodotto

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="Prodotti di Gestione API nel portale":::


## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

## <a name="create-and-publish-a-product"></a>Creare e pubblicare un prodotto

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al portale di Azure e passare all'istanza di Gestione API.
1. Nel riquadro di spostamento a sinistra selezionare **Prodotti** >  **+ Aggiungi**.
1.  Nella finestra **Aggiungi prodotto** immettere i valori descritti nella tabella seguente per creare il prodotto.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Aggiungere un prodotto nel portale":::

    | Nome                     | Descrizione                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome visualizzato             | Nome che dovrà essere visualizzato nel [portale per sviluppatori](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Descrizione              | Consente di specificare informazioni sul prodotto, ad esempio lo scopo, le API a cui fornisce l'accesso e altri dettagli.                                                                                                                                               |
    | State                    | Selezionare **Pubblicato** se si vuole pubblicare il prodotto. Per poter chiamare le API in un prodotto, il prodotto deve essere pubblicato. Per impostazione predefinita, i nuovi prodotti non sono pubblicati e sono visibili solo agli utenti nel gruppo **Amministratori**.                                                                                      |
    | Richiede la sottoscrizione    | Consente di specificare se un utente deve eseguire la sottoscrizione per usare il prodotto.                                                                                                                                                                                                                                   |
    | Richiede approvazione        | Specificare se si preferisce che i tentativi di sottoscrizione del prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore. Se l'opzione non è selezionata, i tentativi di sottoscrizione vengono approvati automaticamente.                                                                                                                         |
    | Limite per il numero di sottoscrizioni | Facoltativamente, limitare il numero di più sottoscrizioni simultanee.                                                                                                                                                                                                                                |
    | Note legali              | È possibile includere le condizioni per l'utilizzo del prodotto che i sottoscrittori devono accettare per usare il prodotto.                                                                                                                                                                                                             |
    | API                     | Selezionare una o più API. È anche possibile aggiungere le API dopo la creazione del prodotto. Per altre informazioni, vedere [Aggiungere API a un prodotto](#add-apis-to-a-product) più avanti in questo articolo. |

3. Fare clic su **Crea** per creare il nuovo prodotto.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per iniziare a usare interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Per creare un prodotto, eseguire il comando [AZ gestione API Product create](/cli/azure/apim/product#az_apim_product_create) :

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

È possibile specificare diversi valori per il prodotto:

   | Parametro | Descrizione |
   |-----------|-------------|
   | `--product-name` | Nome che dovrà essere visualizzato nel [portale per sviluppatori](api-management-howto-developer-portal.md). |
   | `--description`  | Consente di specificare informazioni sul prodotto, ad esempio lo scopo, le API a cui fornisce l'accesso e altri dettagli. |
   | `--state`        | Selezionare **pubblicato** se si desidera pubblicare il prodotto. Per poter chiamare le API in un prodotto, il prodotto deve essere pubblicato. Per impostazione predefinita, i nuovi prodotti non sono pubblicati e sono visibili solo agli utenti nel gruppo **Amministratori**. |
   | `--subscription-required` | Consente di specificare se un utente deve eseguire la sottoscrizione per usare il prodotto. |
   | `--approval-required` | Specificare se si preferisce che i tentativi di sottoscrizione del prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore. Se l'opzione non è selezionata, i tentativi di sottoscrizione vengono approvati automaticamente. |
   | `--subscriptions-limit` | Facoltativamente, limitare il numero di più sottoscrizioni simultanee.|
   | `--legal-terms`         | È possibile includere le condizioni per l'utilizzo del prodotto che i sottoscrittori devono accettare per usare il prodotto. |

Per visualizzare i prodotti correnti, usare il comando [AZ gestione API Product List](/cli/azure/apim/product#az_apim_product_list) :

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Per eliminare un prodotto, è possibile usare il comando [AZ gestione API Product Delete](/cli/azure/apim/product#az_apim_product_delete) :

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Aggiungere altre configurazioni

Continuare a configurare il prodotto dopo averlo salvato. Nell'istanza di Gestione API selezionare il prodotto dalla finestra **Prodotti**. Aggiungere o aggiornare:

|Elemento   |Descrizione  |
|---------|---------|
|Impostazioni     |    Metadati e stato del prodotto     |
|API     |  API associata a un prodotto       |
|[Criteri](api-management-howto-policies.md)     |  Criteri applicati alle API del prodotto      |
|Controllo di accesso     |  Visibilità del prodotto per sviluppatori o utenti guest       |
|[Sottoscrizioni](api-management-subscriptions.md)    |    Sottoscrittori del prodotto     |

## <a name="add-apis-to-a-product"></a>Aggiungere API a un prodotto

I prodotti sono associazioni di una o più API. È possibile includere diverse API e proporle agli sviluppatori tramite il portale per sviluppatori. Durante la creazione del prodotto, è possibile aggiungere una o più API esistenti. È anche possibile aggiungere API al prodotto in un secondo momento, dalla pagina **Impostazioni** dei prodotti o durante la creazione di un'API.

Per avere accesso all'API, gli sviluppatori devono prima sottoscrivere un prodotto. In questo modo ottengono una chiave di sottoscrizione valida per tutte le API nel prodotto. Se si è creata l'istanza di Gestione API, si è già un amministratore e la sottoscrizione a ogni prodotto è stata effettuata per impostazione predefinita.

### <a name="add-an-api-to-an-existing-product"></a>Aggiungere un'API a un prodotto esistente

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel riquadro di spostamento a sinistra dell'istanza di Gestione API selezionare **Prodotti**.
1. Selezionare un prodotto, quindi selezionare **API**.
1. Selezionare **+ Aggiungi**.
1. Selezionare una o più API e quindi fare clic su **Seleziona**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Aggiungere API a un prodotto esistente":::

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Per visualizzare le API gestite, usare il comando [AZ gestione API API List](/cli/azure/apim/api#az_apim_api_list) :

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Per aggiungere un'API al prodotto, eseguire il comando [AZ gestione API Product API Add](/cli/azure/apim/product/api#az_apim_product_api_add) :

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Verificare l'aggiunta usando il comando [AZ gestione API Product API List](/cli/azure/apim/product/api#az_apim_product_api_list) :

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

È possibile rimuovere un'API da un prodotto usando il comando [AZ gestione API Product API Delete](/cli/azure/apim/product/api#az_apim_product_api_delete) :

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> È possibile creare o aggiornare la sottoscrizione utente di un prodotto con chiavi di sottoscrizione personalizzate tramite l'[API REST](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) o un comando PowerShell.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare e pubblicare un prodotto
> * Aggiungere un'API al prodotto

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Creare un'API vuota e simulare le risposte di un'API](mock-api-responses.md)
