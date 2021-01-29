---
title: Configurare un ambiente di staging in Azure Spring Cloud | Microsoft Docs
description: Informazioni su come usare la distribuzione blu/verde con Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8cae73e03fee0b59be0c7596f0783570ac14f6ee
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053064"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurare un ambiente di staging nel cloud Spring di Azure

**Questo articolo si applica a:** ✔️ Java

Questo articolo illustra come configurare una distribuzione di gestione temporanea usando il modello di distribuzione Blue-Green nel cloud Spring di Azure. La distribuzione di tipo blu-verde è un modello di recapito continuo Azure DevOps che si basa sul mantenimento di una versione esistente (blu) attiva mentre viene distribuita una versione nuova (verde). Questo articolo illustra come inserire la distribuzione di gestione temporanea in produzione senza modificare direttamente la distribuzione di produzione.

## <a name="prerequisites"></a>Prerequisiti

* Un'applicazione in esecuzione.  Vedere [Guida introduttiva: distribuire la prima applicazione Azure Spring cloud](spring-cloud-quickstart.md).
* [Estensione](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview) dell'interfaccia della riga di comando di Azure

Se si vuole usare un'applicazione diversa per questo esempio, è necessario apportare una semplice modifica in una parte pubblica dell'applicazione.  Questa modifica differenzia la distribuzione di staging dalla produzione.

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire le istruzioni riportate in questo articolo.  Con gli strumenti di Azure preinstallati e comuni, incluse le versioni più recenti di Git, JDK, Maven e l'interfaccia della riga di comando di Azure. Se è stato effettuato l'accesso alla sottoscrizione di Azure, avviare il [Azure cloud Shell](https://shell.azure.com).  Per altre informazioni, vedere [Panoramica di Azure cloud Shell](../cloud-shell/overview.md).

Per configurare un ambiente di staging nel cloud Spring di Azure, seguire le istruzioni riportate nelle sezioni successive.

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Visualizza le app e le distribuzioni

Visualizzare le app distribuite usando le procedure seguenti.

1. Passare all'istanza di Azure Spring cloud nella portale di Azure.

1. Dal riquadro di spostamento a sinistra aprire **distribuzioni**.

    [![Distribuzione-deprecare](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Aprire il pannello "app" per visualizzare le app per l'istanza del servizio.

    [![App-Dashboard](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. È possibile fare clic su un'app e visualizzare i dettagli.

    [![App-Panoramica](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Aprire il pannello **distribuzioni** per visualizzare tutte le distribuzioni dell'app. La griglia di distribuzione indica se la distribuzione è di produzione o di gestione temporanea.

    [![Dashboard distribuzioni](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. È possibile fare clic sul nome della distribuzione per visualizzare la panoramica della distribuzione. In questo caso l'unica distribuzione è denominata *default*.

    [![Panoramica delle distribuzioni](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Creare una distribuzione di staging

1. Nell'ambiente di sviluppo locale, apportare una piccola modifica all'applicazione. Questa operazione consente di distinguere facilmente le due distribuzioni. Per compilare il pacchetto jar, eseguire il comando seguente: 

    ```console
    mvn clean package -DskipTests
    ```

1. Nell'interfaccia della riga di comando di Azure creare una nuova distribuzione e assegnarle il nome della distribuzione di staging "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app default -n green --jar-path gateway/target/gateway.jar
    ```

1. Al termine della distribuzione dell'interfaccia della riga di comando, accedere alla pagina dell'app dal **Dashboard dell'applicazione** e visualizzare tutte le istanze nella scheda **distribuzioni** a sinistra.

   [![Dashboard distribuzioni dopo la distribuzione verde](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> Lo stato di individuazione è *OUT_OF_SERVICE* in modo che il traffico non venga indirizzato a questa distribuzione prima del completamento della verifica.

## <a name="verify-the-staging-deployment"></a>Verificare la distribuzione di staging

Per verificare che lo sviluppo di gestione temporanea verde funzioni correttamente:
1. passare a **distribuzioni** e fare clic sulla `green` **distribuzione di gestione temporanea**.
1. Nella pagina **Panoramica** fare clic sull' **endpoint di test**.
1. Verrà aperta la compilazione di gestione temporanea che mostra le modifiche.

>[!TIP]
> * Verificare che l'endpoint di test termini con una barra (/) per assicurarsi che il file CSS sia caricato correttamente.  
> * Se il browser richiede di immettere le credenziali di accesso per visualizzare la pagina, usare [Decodifica URL](https://www.urldecoder.org/) per decodificare l'endpoint di test. La decodifica URL restituisce un URL nel formato "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green".  Usare questo modulo per accedere all'endpoint.

>[!NOTE]    
> Le impostazioni del server di configurazione si applicano sia all'ambiente di gestione temporanea che alla produzione. Se ad esempio si imposta il percorso del contesto ( `server.servlet.context-path` ) per il gateway app nel server di configurazione come *somepath*, il percorso della distribuzione verde diventa "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green/somepath/...".
 
 Se a questo punto si visita il gateway app pubblico, viene visualizzata la pagina precedente senza la nuova modifica.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Impostare la distribuzione verde come ambiente di produzione

1. Una volta verificata la modifica nell'ambiente di gestione temporanea, è possibile eseguirne il push in produzione. Tornare alla **gestione della distribuzione** e selezionare l'applicazione attualmente in `Production` .

1. Fare clic sui puntini di sospensione dopo lo **stato della registrazione** e impostare la compilazione di produzione su `staging` .

   [Distribuzione di ![ set di gestione temporanea](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Tornare alla pagina di **gestione della distribuzione** .  `green`Lo stato della distribuzione della distribuzione dovrebbe essere visualizzato. Questa è ora la build di produzione in esecuzione.

   [![Risultato della distribuzione del set di gestione temporanea](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. Copiare e incollare l'URL in una nuova finestra del browser e visualizzare la pagina nuova applicazione con le modifiche apportate.

>[!NOTE]
> Dopo aver impostato la distribuzione verde come ambiente di produzione, la distribuzione precedente diventa la distribuzione di gestione temporanea.

## <a name="modify-the-staging-deployment"></a>Modificare la distribuzione di staging

Se non si è soddisfatti della modifica, è possibile modificare il codice dell'applicazione, compilare un nuovo pacchetto jar e caricarlo nella distribuzione verde usando l'interfaccia della riga di comando di Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminare la distribuzione di gestione temporanea

Per eliminare la distribuzione di staging dalla porta di Azure, passare alla pagina distribuzione di staging e quindi selezionare il pulsante **Elimina** .

In alternativa, eliminare la distribuzione di staging dall'interfaccia della riga di comando di Azure eseguendo il comando seguente:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione continua/distribuzione continua per il cloud di Azure Spring](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)