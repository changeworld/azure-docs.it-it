---
title: Raccogliere Spring Cloud metriche dell'interruttore resiliente4J con micrometro
description: Come raccogliere le Spring Cloud dell'interruttore resilience4J con Micrometer in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fedebd9182c168b9b7c455d5f6726e66720e0a8b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479160"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Raccogliere Spring Cloud metriche dell'interruttore resiliente4J con micrometro (anteprima)

Questo documento illustra come raccogliere Spring Cloud metriche dell'interruttore di circuito Resilience4j con Application Insights'agente java in-process. Con questa funzionalità è possibile monitorare le metriche dell'interruttore del circuito resilience4j Application Insights con Micrometer.

Per illustrare il funzionamento, viene utilizzata la [demo spring-cloud-circuit-breaker-demo.](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo)

## <a name="prerequisites"></a>Prerequisiti

* Abilitare Java In-Process Agent da [Java In-Process Agent per Application Insights guida](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Abilitare la raccolta di dimensioni per le metriche resilience4j [dalla Application Insights .](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)

* Installare Git, Maven e Java, se non sono già in uso nel computer di sviluppo.

## <a name="build-and-deploy-apps"></a>Compilare e distribuire app

La procedura seguente compila e distribuisce le app.

1. Clonare e compilare il repository demo.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Creare applicazioni con endpoint

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Distribuire applicazioni.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Includere la dipendenza necessaria per Resilience4j:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * Il codice del cliente deve usare l'API di , che viene implementata come creata automaticamente quando si include un Spring Cloud `CircuitBreakerFactory` `bean` interruttore di avvio. Per informazioni [dettagliate, Spring Cloud interruttore.](https://spring.io/projects/spring-cloud-circuitbreaker#overview)
>
> * Le 2 dipendenze seguenti sono in conflitto con i pacchetti resilient4j precedenti.  Assicurarsi che il cliente non li includa.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Passare all'URL fornito dalle applicazioni gateway e accedere all'endpoint da [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) come segue:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Individuare le metriche di Resilence4j dal portale

1. Selezionare il **pannello Application Insights** nel Azure Spring Cloud e fare clic su **Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Selezionare **Metriche** nella **Application Insights** pagina.  Selezionare **azure.applicationinsights in** Spazio **dei nomi delle metriche.**  Selezionare **anche** resilience4j_circuitbreaker_buffered_calls metriche con **Media.**

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Selezionare **resilience4j_circuitbreaker_calls** metriche e **Media.**

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Selezionare **resilience4j_circuitbreaker_calls** metriche e **Media.**  Fare **clic su Aggiungi** filtro e quindi selezionare **createNewAccount** come nome.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Selezionare **resilience4j_circuitbreaker_calls** metriche e **Media.**  Fare quindi **clic su Applica suddivisione** e selezionare **il tipo**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Selezionare **resilience4j_circuitbreaker_calls**, '**resilience4j_circuitbreaker_buffered_calls** e **resilience4j_circuitbreaker_slow_calls** metriche con **Media**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Vedi anche

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Analisi distribuita](spring-cloud-howto-distributed-tracing.md)
* [Dashboard interruttore](spring-cloud-tutorial-circuit-breaker.md)
