---
title: 'Guida introduttiva: la prima query Java'
description: In questa Guida introduttiva si seguono i passaggi per abilitare i pacchetti Maven del grafico risorse per Java ed eseguire la prima query.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223971"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Guida introduttiva: eseguire la prima query di Resource Graph con Java

Il primo passaggio per usare Azure Resource Graph consiste nel verificare che siano installati i pacchetti Maven necessari per Java. Questa Guida introduttiva illustra il processo di aggiunta dei pacchetti Maven all'installazione di Java.

Al termine di questo processo, i pacchetti Maven sono stati aggiunti all'installazione di Java ed è stata eseguita la prima query del grafico risorse.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Verificare che sia installata la versione più recente dell'interfaccia della riga di comando di Azure (almeno **2.21.0**). Se non è ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

  > [!NOTE]
  > L'interfaccia della riga di comando di Azure è necessaria per abilitare Azure SDK per Java per l'uso dell' **autenticazione basata sull'interfaccia** della riga di comando negli esempi seguenti. Per informazioni su altre opzioni, vedere [libreria client di identità di Azure per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versione
  8.

- [Apache Maven](https://maven.apache.org/), versione 3,6 o successiva.

## <a name="create-the-resource-graph-project"></a>Creare il progetto Resource Graph

Per abilitare Java per eseguire query su Graph di risorse di Azure, creare e configurare una nuova applicazione con Maven e installare i pacchetti Maven necessari.

1. Inizializzare una nuova applicazione Java denominata "argQuery" con un [archetipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Modificare le directory nella nuova cartella di progetto `argQuery` e aprirlo `pom.xml` nell'editor preferito. Aggiungere i `<dependency>` nodi seguenti nel nodo esistente `<dependencies>` :

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. Nel `pom.xml` file aggiungere il nodo seguente nel `<properties>` `<project>` nodo di base per aggiornare le versioni di origine e di destinazione:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Nel `pom.xml` file aggiungere il seguente `<build>` nodo sotto il `<project>` nodo di base per configurare l'obiettivo e la classe principale per l'esecuzione del progetto.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Sostituire il valore predefinito `App.java` in `\argQuery\src\main\java\com\Fabrikam` con il codice seguente e salvare il file aggiornato:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. Compilare l' `argQuery` applicazione console:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Eseguire la prima query di Resource Graph

Con l'applicazione console Java compilata, è il momento di provare una semplice query del grafico risorse. La query restituisce le prime cinque risorse di Azure con il **nome** e il **tipo di risorsa** di ogni risorsa.

In ogni chiamata a `argQuery` vengono usate variabili che è necessario sostituire con i propri valori:

- `{subscriptionId}`: sostituire con l'ID sottoscrizione
- `{query}` -Sostituire con la query Graph di risorse di Azure

1. Usare l'interfaccia della riga di comando di Azure per l'autenticazione con `az login` .

1. Passare alla cartella del `argQuery` progetto creata con il `mvn -B archetype:generate` comando precedente.

1. Eseguire la prima query di Azure Resource Graph usando Maven per compilare l'applicazione console e passare gli argomenti. La `exec.args` proprietà identifica gli argomenti per spazi. Per identificare la query come singolo argomento, viene eseguito il wrapping con virgolette singole ( `'` ).

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Poiché questo esempio di query non prevede un modificatore del tipo di ordinamento, ad esempio `order by`, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta.

1. Modificare l'argomento in `argQuery.exe` e modificare la query nella `order by` proprietà **Name** :

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Come nella prima query, se si esegue questa query più volte, è probabile che venga restituito un set di risorse diverso per ogni richiesta. L'ordine dei comandi della query è importante. In questo esempio `order by` segue `limit`. Questo ordine dei comandi prima limita i risultati della query e successivamente li ordina.

1. Impostare il parametro finale su `argQuery.exe` e cambiare la query per applicare prima `order by` alla proprietà **Name** e quindi `limit` ai primi cinque risultati:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Quando la query finale viene eseguita più volte, presupponendo che non vengano apportate modifiche all'ambiente, i risultati restituiti saranno coerenti e ordinati in base alla proprietà **Name**, ma ancora limitati ai primi cinque risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera rimuovere l'applicazione console Java e i pacchetti installati, è possibile eliminare la `argQuery` cartella del progetto.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata creata un'applicazione console Java con i pacchetti di grafici di risorse richiesti ed è stata eseguita la prima query. Per altre informazioni sul linguaggio di Resource Graph, passare alla pagina dei dettagli del linguaggio di query.

> [!div class="nextstepaction"]
> [Altre informazioni sul linguaggio di query](./concepts/query-language.md)