---
title: 'Avvio rapido: Libreria client di gestione di Azure per Python'
description: Questo argomento di avvio rapido contiene un'introduzione alla libreria client di gestione di Azure per Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fb908cdcf3e235654effc043de29e599a48179d4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879667"
---
[Documentazione di riferimento](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [Pacchetto (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Prerequisiti di Python

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare una nuova applicazione Python nell'editor o nell'IDE preferito e passare al progetto in una finestra della console.

### <a name="install-the-client-library"></a>Installare la libreria client

È possibile installare la libreria client con:

```console
pip install azure-mgmt-cognitiveservices
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

### <a name="import-libraries"></a>Importare le librerie

Aprire lo script Python e importare le librerie seguenti.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere i campi seguenti alla radice dello script e inserire i relativi valori usando l'entità servizio creata e le informazioni sull'account Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Aggiungere quindi il codice seguente per creare un oggetto **CognitiveServicesManagementClient**. Questo oggetto è necessario per tutte le operazioni di gestione di Azure.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Creare una risorsa di Servizi cognitivi (Python)

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare la funzione **Crea**. Questa funzione aggiunge una nuova risorsa fatturabile al gruppo di risorse che viene passato. Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al piano tariffario (o SKU) e a una località di Azure. La funzione seguente accetta tutti questi argomenti e crea una risorsa.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Scegliere un servizio e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni verranno usate come parametri per la creazione della risorsa. La funzione seguente elenca i tipi di Servizi cognitivi disponibili.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Visualizzare le risorse

Per visualizzare tutte le risorse nell'account Azure (in tutti i gruppi di risorse), usare la funzione seguente:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminare una risorsa

La funzione seguente elimina la risorsa specificata dal gruppo di risorse specificato.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Chiamare le funzioni di gestione

Aggiungere il codice seguente alla fine dello script per chiamare le funzioni indicate sopra. Questo codice elenca le risorse disponibili, crea una risorsa di esempio, elenca le risorse di cui si è proprietari e quindi elimina la risorsa di esempio.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla riga di comando con il comando `python`.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Vedere anche

* Vedere **[autenticare le richieste ai servizi cognitivi di Azure](../../authentication.md)** per informazioni su come lavorare in modo sicuro con servizi cognitivi.
* Vedere **[che cosa sono i servizi cognitivi di Azure?](../../what-are-cognitive-services.md)** per ottenere un elenco di categorie diverse all'interno di servizi cognitivi.
* Vedere **[supporto del linguaggio naturale](../../language-support.md)** per visualizzare l'elenco dei linguaggi naturali supportati da servizi cognitivi.
* Vedere **[usare servizi cognitivi come contenitori](../../cognitive-services-container-support.md)** per comprendere come usare i servizi cognitivi locali.
* Vedere **[pianificare e gestire i costi per i servizi cognitivi](../../plan-manage-costs.md)** per stimare i costi dell'uso di servizi cognitivi.
* Per ulteriori informazioni sull'SDK di gestione, vedere la **[documentazione di riferimento di Azure Management SDK](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)** .
