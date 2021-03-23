---
title: Configurare un ambiente di staging in Azure Spring Cloud | Microsoft Docs
description: Informazioni su come usare la distribuzione blu/verde con Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0a8536deac0103215cf362c07eb54bbf84701a6b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878000"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurare un ambiente di staging nel cloud Spring di Azure

**Questo articolo si applica a:** ✔️ Java

Questo articolo illustra come configurare una distribuzione di gestione temporanea usando il modello di distribuzione Blue-Green nel cloud Spring di Azure. La distribuzione blu-verde è un modello di recapito continuo di Azure DevOps che si basa sulla conservazione di una versione esistente (blu) in tempo reale mentre viene distribuito un nuovo (verde). Questo articolo illustra come inserire la distribuzione di gestione temporanea in produzione senza modificare la distribuzione di produzione.

## <a name="prerequisites"></a>Prerequisiti

* Istanza del cloud Spring di Azure in un piano tariffario standard
* [Estensione cloud Spring di Azure](/cli/azure/azure-cli-extensions-overview) per l'interfaccia della riga di comando di Azure

Questo articolo usa un'applicazione creata con Spring Initializr. Se si vuole usare un'applicazione diversa per questo esempio, è necessario apportare una semplice modifica in una parte pubblica dell'applicazione per distinguere la distribuzione di gestione temporanea dalla produzione.

>[!TIP]
> [Azure cloud Shell](https://shell.azure.com) è una shell interattiva gratuita che può essere usata per eseguire le istruzioni riportate in questo articolo.  Con gli strumenti di Azure preinstallati e comuni, incluse le versioni più recenti di Git, JDK, Maven e l'interfaccia della riga di comando di Azure. Se è stato effettuato l'accesso alla sottoscrizione di Azure, avviare l'istanza di Cloud Shell. Per altre informazioni, vedere [Panoramica di Azure cloud Shell](../cloud-shell/overview.md).

Per configurare la distribuzione Blue-Green nel cloud Spring di Azure, seguire le istruzioni riportate nelle sezioni successive.

## <a name="install-the-azure-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando di Azure

Installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Preparare l'app e le distribuzioni
Per compilare l'applicazione, attenersi alla procedura seguente:

1. Generare il codice per l'app di esempio usando Spring Initializr con [questa configurazione](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Scaricare il codice.
3. Aggiungere il file di origine HelloController. Java seguente alla cartella `\src\main\java\com\example\hellospring\` :

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud!"; 
     } 

   } 
   ```
4. Compilare il file con estensione jar:

   ```azurecli
   mvn clean packge -DskipTests
   ```
5. Creare l'app nell'istanza di Azure Spring cloud:

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```
6. Distribuire l'app nel cloud Spring di Azure:

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```
7. Modificare il codice per la distribuzione di gestione temporanea:

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
     } 

   } 
   ```
8. Ricompilare il file con estensione jar:

   ```azurecli
   mvn clean packge -DskipTests
   ```
9. Creare la distribuzione verde: 

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
   ```

## <a name="view-apps-and-deployments"></a>Visualizza le app e le distribuzioni

Visualizzare le app distribuite usando la procedura seguente:

1. Passare all'istanza di Azure Spring cloud nella portale di Azure.

1. Dal riquadro sinistro aprire il riquadro **app** per visualizzare le app per l'istanza del servizio.

   ![Screenshot del riquadro Apri app.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. È possibile selezionare un'app e visualizzare i dettagli.

   ![Screenshot dei dettagli per un'app.](media/spring-cloud-blue-green-staging/app-overview.png)

1. Aprire **distribuzioni** per visualizzare tutte le distribuzioni dell'app. Nella griglia vengono visualizzate le distribuzioni di produzione e di gestione temporanea.

   ![Screenshot che mostra le distribuzioni di app elencate.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Selezionare l'URL per aprire l'applicazione attualmente distribuita.
    
   ![Screenshot che mostra l'U R L per l'applicazione distribuita.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Selezionare **produzione** nella colonna **stato** per visualizzare l'app predefinita.
    
   ![Screenshot che mostra l'U R L per l'app predefinita.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Selezionare **gestione temporanea** nella colonna **stato** per visualizzare l'app di staging.
    
   ![Screenshot che mostra l'U R L per l'app di staging.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Verificare che l'endpoint di test termini con una barra (/) per assicurarsi che il file CSS sia caricato correttamente.  
> * Se il browser richiede di immettere le credenziali di accesso per visualizzare la pagina, usare [Decodifica URL](https://www.urldecoder.org/) per decodificare l'endpoint di test. La decodifica URL restituisce un URL nel formato *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green*. Usare questo formato per accedere all'endpoint.

>[!NOTE]    
> Le impostazioni del server di configurazione si applicano sia all'ambiente di gestione temporanea che all'ambiente di produzione. Ad esempio, se si imposta il percorso del contesto (*Server. servlet. Context-path*) per il gateway app nel server di configurazione come *somepath*, il percorso della distribuzione verde cambierà in *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green/somepath/...*.
 
Se a questo punto si visita il gateway app pubblico, viene visualizzata la pagina precedente senza la nuova modifica.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Impostare la distribuzione verde come ambiente di produzione

1. Una volta verificata la modifica nell'ambiente di gestione temporanea, è possibile eseguirne il push in produzione. Nella pagina   >  **distribuzioni** di app selezionare l'applicazione attualmente in **produzione**.

1. Selezionare i puntini di sospensione dopo **lo stato di registrazione** della distribuzione verde, quindi selezionare **Imposta come produzione**. 

   ![Screenshot che mostra le selezioni per l'impostazione della compilazione di gestione temporanea nell'ambiente di produzione.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Verificare che l'URL dell'app visualizzi le modifiche.

   ![Screenshot che mostra l'U R L dell'app ora in produzione.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Dopo aver impostato la distribuzione verde come ambiente di produzione, la distribuzione precedente diventa la distribuzione di gestione temporanea.

## <a name="modify-the-staging-deployment"></a>Modificare la distribuzione di staging

Se non si è soddisfatti della modifica, è possibile modificare il codice dell'applicazione, compilare un nuovo pacchetto jar e caricarlo nella distribuzione verde usando l'interfaccia della riga di comando di Azure:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminare la distribuzione di gestione temporanea

Per eliminare la distribuzione di staging dalla portale di Azure, passare alla pagina relativa alla distribuzione di staging e selezionare il pulsante **Elimina** .

In alternativa, eliminare la distribuzione di staging dall'interfaccia della riga di comando di Azure eseguendo il comando seguente:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione continua/distribuzione continua per il cloud di Azure Spring](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)