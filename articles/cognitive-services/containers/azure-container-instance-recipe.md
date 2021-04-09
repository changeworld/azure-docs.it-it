---
title: Ricetta dell'istanza di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire i contenitori di servizi cognitivi nell'istanza di contenitore di Azure
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97704843"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Distribuire ed eseguire un contenitore in Istanze di Azure Container

Con i passaggi seguenti è possibile ridimensionare facilmente le applicazioni di servizi cognitivi di Azure nel cloud con [istanze di contenitore](../../container-instances/index.yml)di Azure. Il contenitore consente di concentrarsi sulla creazione di applicazioni anziché sulla gestione dell'infrastruttura. Per altre informazioni sull'uso dei contenitori, vedere [funzionalità e vantaggi](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Prerequisiti

La ricetta funziona con qualsiasi contenitore di servizi cognitivi. Prima di usare la ricetta è necessario creare la risorsa del servizio cognitivo. Ogni servizio cognitivo che supporta i contenitori ha un articolo "How to install" per l'installazione e la configurazione del servizio per un contenitore. Alcuni servizi richiedono un file o un set di file come input per il contenitore, è importante comprendere che il contenitore è stato usato correttamente prima di usare questa soluzione.

* Una risorsa di Azure per il servizio cognitivo di Azure in uso.
* URL dell' **endpoint** di servizio cognitivo: esaminare il "come installare" del servizio specifico per il contenitore, trovare la posizione in cui si trova l'URL dell'endpoint all'interno del portale di Azure e l'aspetto di un esempio corretto dell'URL. Il formato esatto può variare da servizio a servizio.
* **Chiave** del servizio cognitivo: le chiavi si trovano nella pagina **chiavi** della risorsa di Azure. È necessaria solo una delle due chiavi. La chiave è una stringa di 32 caratteri alfanumerici.

* Un singolo contenitore di servizi cognitivi nell'host locale (computer). Verificare che sia possibile:
  * Estrarre l'immagine con un `docker pull` comando.
  * Eseguire correttamente il contenitore locale con tutte le impostazioni di configurazione necessarie con un `docker run` comando.
  * Chiamare l'endpoint del contenitore, ottenendo una risposta di HTTP 2xx e una risposta JSON.

Tutte le variabili racchiuse tra parentesi angolari `<>` devono essere sostituite con valori personalizzati. Questa sostituzione include le parentesi angolari.

> [!IMPORTANT]
> Il contenitore LUIS richiede un `.gz` file di modello di cui è stato eseguito il pull in fase di esecuzione. Il contenitore deve essere in grado di accedere al file del modello tramite un montaggio del volume dall'istanza del contenitore. Per caricare un file di modello, attenersi alla seguente procedura:
> 1. [Creare una condivisione file di Azure](../../storage/files/storage-how-to-create-file-share.md). Prendere nota del nome dell'account di archiviazione di Azure, della chiave e del nome della condivisione file, perché saranno necessari in un secondo momento.
> 2. [esportare il modello Luis (app in pacchetto) dal portale Luis](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. Nella portale di Azure passare alla pagina **Panoramica** della risorsa dell'account di archiviazione e selezionare **condivisioni file**. 
> 4. Selezionare il nome della condivisione file creata di recente, quindi selezionare **carica**. Caricare quindi l'app in pacchetto. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Usare l'istanza di contenitore

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Selezionare la **Panoramica** e copiare l'indirizzo IP. Si tratta di un indirizzo IP numerico, ad esempio `55.55.55.55` .
1. Aprire una nuova scheda del browser e usare l'indirizzo IP, ad esempio, `http://<IP-address>:5000 (http://55.55.55.55:5000` . Verrà visualizzato il home page del contenitore, che informa che il contenitore è in esecuzione.

    ![Home page del contenitore](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Selezionare **Descrizione API servizio** per visualizzare la pagina di spavalderia per il contenitore.

1. Selezionare una delle API **post** e selezionare **prova**.  I parametri vengono visualizzati con l'input. Inserire i parametri.

1. Selezionare **Execute (Esegui** ) per inviare la richiesta all'istanza del contenitore.

    Sono stati creati e usati i contenitori di servizi cognitivi nell'istanza di contenitore di Azure.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Se si sta eseguendo il Analisi del testo per il contenitore di integrità, usare l'URL seguente per inviare le query: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
