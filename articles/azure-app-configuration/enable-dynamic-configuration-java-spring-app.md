---
title: Usare la configurazione dinamica in un'app Spring Boot
titleSuffix: Azure App Configuration
description: Informazioni su come aggiornare dinamicamente i dati di configurazione per le app Spring Boot
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 590f221b0a4980d462267dd8c3a73ca7d02583fd
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625518"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Esercitazione: Usare la configurazione dinamica in un'app Java Spring

La configurazione dell'app include due librerie per la primavera. `spring-cloud-azure-appconfiguration-config` richiede Spring boot e prende una dipendenza da `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` richiede Spring Web insieme a Spring boot. Entrambe le librerie supportano l'attivazione manuale per verificare la presenza di valori di configurazione aggiornati. `spring-cloud-azure-appconfiguration-config-web` aggiunge anche il supporto per il controllo automatico dell'aggiornamento della configurazione.

L'aggiornamento consente di aggiornare i valori di configurazione senza dover riavviare l'applicazione, anche se tutti i fagioli in verranno `@RefreshScope` ricreati. La libreria client memorizza nella cache un ID hash delle configurazioni attualmente caricate per evitare un numero eccessivo di chiamate all'archivio di configurazione. Finché il valore memorizzato nella cache non scade, l'operazione di aggiornamento non aggiorna il valore, neanche se è cambiato nell'archivio di configurazione. Il tempo di scadenza predefinito per ogni richiesta è 30 secondi. È possibile sostituire tale valore se necessario.

`spring-cloud-azure-appconfiguration-config-web`l'aggiornamento automatico viene attivato in base all'attività, in particolare Spring Web `ServletRequestHandledEvent` . Se `ServletRequestHandledEvent` non viene attivato un, `spring-cloud-azure-appconfiguration-config-web` l'aggiornamento automatico non attiverà un aggiornamento anche se la scadenza della cache è scaduta.

## <a name="use-manual-refresh"></a>Usare l'aggiornamento manuale

La configurazione dell'app espone `AppConfigurationRefresh` che può essere usata per verificare se la cache è scaduta e se è scaduta attivare un aggiornamento.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh``refreshConfigurations()`restituisce un valore `Future` che è true se è stato attivato un aggiornamento e false in caso contrario. False indica che l'ora di scadenza della cache non è scaduta, che non sono state apportate modifiche o che un altro thread sta attualmente verificando la presenza di un aggiornamento.

## <a name="use-automated-refresh"></a>Usare l'aggiornamento automatico

Per usare l'aggiornamento automatico, iniziare con un'app Spring Boot che usa Configurazione app, ad esempio l'app creata seguendo la guida di avvio rapido per la creazione di un'app [Spring Boot con Configurazione app](quickstart-java-spring-app.md).

Aprire quindi il file *pom.xml* in un editor di testo e aggiungere un elemento `<dependency>` per `spring-cloud-azure-appconfiguration-config-web`.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Eseguire e testare l'app in locale

1. Compilare l'applicazione Spring Boot con Maven ed eseguirla.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Aprire una finestra del browser e passare all'URL: `http://localhost:8080`.  Verrà visualizzato il messaggio associato alla chiave.

    È anche possibile usare *curl* per testare l'applicazione, ad esempio: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Per testare la configurazione dinamica, aprire il portale di Configurazione app di Azure associato all'applicazione. Selezionare **Esplora configurazioni** e aggiornare il valore della chiave visualizzata, ad esempio:

    | Chiave | valore |
    |---|---|
    | application/config.message | Hello - Updated |

1. Aggiornare la pagina del browser per visualizzare il nuovo messaggio.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'app Spring Boot per aggiornare in modo dinamico le impostazioni di configurazione da Configurazione app. Per informazioni su come usare un'identità gestita di Azure per semplificare l'accesso a Configurazione app, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)
