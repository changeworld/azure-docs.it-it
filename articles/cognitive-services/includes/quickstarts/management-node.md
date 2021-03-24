---
title: 'Avvio rapido: Libreria client di gestione di Azure per Node.js'
description: Questo argomento di avvio rapido contiene un'introduzione alla libreria client di gestione di Azure per Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 41f6c8e260968eacd04249b3f887d4865907df0d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879676"
---
[Documentazione di riferimento](/javascript/api/@azure/arm-cognitiveservices/) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Pacchetto (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>Prerequisiti di JavaScript

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/).
* Versione corrente di [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app. 

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`. 

```console
npm init
```

Prima di procedere, creare un file denominato _index.js_.

### <a name="install-the-client-library"></a>Installare la libreria client

Installare i pacchetti NPM seguenti:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

### <a name="import-libraries"></a>Importare le librerie

Aprire lo script _index.js_ e importare le librerie seguenti.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere i campi seguenti alla radice dello script e inserire i relativi valori usando l'entità servizio creata e le informazioni sull'account Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Aggiungere quindi la funzione `quickstart` seguente per gestire le operazioni principali del programma. Il primo blocco di codice crea un oggetto **CognitiveServicesManagementClient** usando le variabili di credenziali immesse sopra. Questo oggetto è necessario per tutte le operazioni di gestione di Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Chiamare le funzioni di gestione

Aggiungere il codice seguente alla fine della funzione `quickstart` per elencare le risorse disponibili, creare una risorsa di esempio, elencare le risorse di cui si è proprietari e quindi eliminare la risorsa di esempio. Queste funzioni verranno definite nei passaggi seguenti.

## <a name="create-a-cognitive-services-resource-nodejs"></a>Creare una risorsa di Servizi cognitivi (Node.js)

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare la funzione **Crea**. Questa funzione aggiunge una nuova risorsa fatturabile al gruppo di risorse che viene passato. Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al piano tariffario (o SKU) e a una località di Azure. La funzione seguente accetta tutti questi argomenti e crea una risorsa.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Scegliere un servizio e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni verranno usate come parametri per la creazione della risorsa. La funzione seguente elenca i tipi di Servizi cognitivi disponibili.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Visualizzare le risorse

Per visualizzare tutte le risorse nell'account Azure (in tutti i gruppi di risorse), usare la funzione seguente:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminare una risorsa

La funzione seguente elimina la risorsa specificata dal gruppo di risorse specificato.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Aggiungere il codice seguente alla fine dello script per chiamare la funzione `quickstart` principale con la gestione degli errori.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Quindi, nella finestra della console, eseguire l'applicazione con il comando `node`.

```console
node index.js
```

## <a name="see-also"></a>Vedere anche

* Vedere **[autenticare le richieste ai servizi cognitivi di Azure](../../authentication.md)** per informazioni su come lavorare in modo sicuro con servizi cognitivi.
* Vedere **[che cosa sono i servizi cognitivi di Azure?](../../what-are-cognitive-services.md)** per ottenere un elenco di categorie diverse all'interno di servizi cognitivi.
* Vedere **[supporto del linguaggio naturale](../../language-support.md)** per visualizzare l'elenco dei linguaggi naturali supportati da servizi cognitivi.
* Vedere **[usare servizi cognitivi come contenitori](../../cognitive-services-container-support.md)** per comprendere come usare i servizi cognitivi locali.
* Vedere **[pianificare e gestire i costi per i servizi cognitivi](../../plan-manage-costs.md)** per stimare i costi dell'uso di servizi cognitivi.
* Per ulteriori informazioni sull'SDK di gestione, vedere la **[documentazione di riferimento di Azure Management SDK](/javascript/api/@azure/arm-cognitiveservices/)** .
