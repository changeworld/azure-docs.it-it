---
title: Alternative per il portale per sviluppatori con hosting self-hosting
titleSuffix: Azure API Management
description: Informazioni sugli approcci alternativi che è possibile usare quando si ospita in modo self-host un portale per sviluppatori in Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: cb14ecd05c1590667ac9b5618b3f0de9da30ce96
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741699"
---
# <a name="alternative-approaches-to-self-host-developer-portal"></a>Approcci alternativi al portale per sviluppatori self-host

Esistono diversi approcci alternativi che è possibile esplorare quando si ospita in modo [self-host un portale per sviluppatori:](developer-portal-self-host.md)

* Usare le build di produzione della finestra di progettazione e dell'editore.

* Usare un'app per le funzioni di Azure per pubblicare il portale.

* Visualizzare i file del portale con una rete per la distribuzione di contenuti (RETE CDN) per ridurre i tempi di caricamento delle pagine.

Questo articolo fornisce informazioni su ognuno di questi approcci. 

Se non è già stato fatto, configurare un [ambiente locale](developer-portal-self-host.md#step-1-set-up-local-environment) per la versione più recente del portale per sviluppatori.

## <a name="build-for-production"></a>Compilare per la produzione

Se si vuole ospitare l'ambiente di sviluppo del portale online per scopi di collaborazione, usare le build di produzione della finestra di progettazione e dell'editore. Le compilazioni di produzione aggregano i file, escludono le mappe di origine e così via.

Creare un bundle nella `./dist/designer` directory eseguendo il comando :

```sh
npm run build-designer
```

Il risultato è un'applicazione a pagina singola, quindi è comunque possibile distribuirla in un host Web statico, ad esempio il Archiviazione BLOB di Azure sito Web statico.

Analogamente, inserire un editore compilato e ottimizzato nella `./dist/publisher` cartella :

```sh
npm run build-publisher
```

## <a name="use-function-app-to-publish-the-portal"></a>Usare l'app per le funzioni per pubblicare il portale

Eseguire il passaggio di pubblicazione nel cloud come alternativa all'esecuzione in locale.

Per implementare la pubblicazione con un'app per le funzioni di Azure, sono necessari i prerequisiti seguenti:

- [Creare una funzione di Azure.](../azure-functions/functions-create-first-azure-function.md) La funzione deve essere una funzione del linguaggio JavaScript.
- Installare Azure Functions Core Tools:
    ```console
    npm install –g azure-function-core-tools
    ```

### <a name="step-1-configure-output-storage"></a>Passaggio 1: Configurare l'archiviazione di output

Caricamento del contenuto direttamente nel sito Web di hosting ("$web" contenitore di archiviazione di output), anziché in una cartella locale. Configurare questa modifica nel `./src/config.publish.json` file:

```json
{
   ...
   "outputBlobStorageContainer": "$web",
   "outputBlobStorageConnectionString": "DefaultEndpointsProtocol=...",
   ...
}
```

### <a name="step-2-build-and-deploy-the-function-app"></a>Passaggio 2: Compilare e distribuire l'app per le funzioni

Nella cartella è presente una funzione trigger HTTP `./examples` di esempio. Per compilarlo e posizionarlo in `./dist/function` , eseguire il comando seguente:

```sh
npm run build-function
```

Accedere quindi all'interfaccia della riga di comando di Azure e distribuirla:

```sh
az login
cd ./dist/function
func azure functionapp publish <function app name>
```

Dopo la distribuzione, è possibile richiamarlo con una chiamata HTTP:

```sh
curl -X POST https://<function app name>.azurewebsites.net/api/publish
```

## <a name="hosting-and-cdn"></a>Hosting e rete CDN

In [un portale per sviluppatori self-host](developer-portal-self-host.md) è consigliabile usare un account di archiviazione di Azure per ospitare il sito Web. È tuttavia possibile pubblicare i file tramite qualsiasi soluzione, inclusi i servizi dei provider di hosting.

È anche possibile eseguire il front-front dei file con una rete per la distribuzione di contenuti (CDN) per ridurre i tempi di caricamento delle pagine. È consigliabile usare [Rete CDN di Azure](https://azure.microsoft.com/services/cdn/).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
