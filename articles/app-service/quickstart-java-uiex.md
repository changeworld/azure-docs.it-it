---
title: "Avvio rapido: Creare un'app Java nel Servizio app di Azure"
description: Distribuire la prima app Hello World Java nel Servizio app di Azure in pochi minuti. Il plug-in di App Web di Azure per Maven semplifica la distribuzione delle app Java.
keywords: azure, servizio app, web app, windows, linux, java, maven, avvio rapido
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 64e4c05e9439c164329dede5d714bec160bc5ae2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050376"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Avvio rapido: Creare un'app Java nel Servizio app di Azure

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione.  Questo argomento di avvio rapido illustra come usare l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) con il [plug-in App Web di Azure per Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) per distribuire un file con estensione jar o war. 

Sono disponibili anche versioni IDE di questo articolo. Vedere [Azure Toolkit for IntelliJ Guida introduttiva](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) o [Azure Toolkit for Eclipse avvio rapido](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

Per eseguire le procedure descritte è necessario:

+ Un <abbr title="Profilo che mantiene le informazioni di fatturazione per l'utilizzo di Azure.">Account Azure</abbr> con un oggetto attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo utente o a un reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versione 8 o 11.

+ [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

Il plug-in Maven usa le credenziali dell'account dall'interfaccia della riga di comando di Azure per la distribuzione nei servizi app. [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli) prima di continuare. Per altre informazioni, vedere [Autenticazione con i plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. creare un'app java

# <a name="java-se"></a>[Java SE](#tab/javase)

Clonare l'esempio di progetto [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot).

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Passare alla directory del progetto completato.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Eseguire il comando Maven seguente nel prompt di Cloud Shell per creare una nuova app denominata `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Passare quindi dalla directory di lavoro alla cartella del progetto:

```bash
cd helloworld
```

---

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. configurare il plug-in Maven

Eseguire il comando Maven seguente per configurare la distribuzione. Questo comando consentirà di configurare il sistema operativo del servizio app, la versione di Java e la versione di Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando viene visualizzata l'opzione **Sottoscrizione**, selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **app Web** , accettare l'opzione predefinita `<create>` premendo invio o selezionando un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo**, selezionare **Windows** immettendo `3`.
1. Quando viene visualizzata l'opzione **Piano tariffario**, selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8**, premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    L'output di riepilogo sarà simile al frammento riportato di seguito.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando viene visualizzata l'opzione **Sottoscrizione**, selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **app Web** , accettare l'opzione predefinita `<create>` premendo invio o selezionando un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo**, selezionare **Windows** immettendo `3`.
1. Quando viene visualizzata l'opzione **Piano tariffario**, selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8**, premendo INVIO.
1. Usare il contenitore Web predefinito, **Tomcat 8.5**, premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    L'output di riepilogo sarà simile al frammento riportato di seguito.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Quando viene visualizzata l'opzione **Sottoscrizione**, selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **app Web** , accettare l'opzione predefinita `<create>` premendo invio o selezionando un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo**, selezionare **Linux** premendo INVIO.
1. Quando viene visualizzata l'opzione **Piano tariffario**, selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8**, premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Quando viene visualizzata l'opzione **Sottoscrizione**, selezionare il valore appropriato di `Subscription` immettendo la stampa del numero all'inizio della riga.
1. Quando viene visualizzata l'opzione **app Web** , accettare l'opzione predefinita `<create>` premendo invio o selezionando un'app esistente.
1. Quando viene visualizzata l'opzione **Sistema operativo**, selezionare **Linux** premendo INVIO.
1. Quando viene visualizzata l'opzione **Piano tariffario**, selezionare **B2** immettendo `2`.
1. Usare la versione predefinita di Java, **Java 8**, premendo INVIO.
1. Usare il contenitore Web predefinito, **Tomcat 8.5**, premendo INVIO.
1. Infine, premere INVIO nell'ultima richiesta per confermare le selezioni.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end

È possibile modificare le configurazioni per il servizio app direttamente nel file `pom.xml`, se necessario. Ecco alcune configurazioni comuni:

Proprietà | Obbligatoria | Descrizione | Versione
---|---|---|---
`<schemaVersion>` | false | Specificare la versione dello schema di configurazione. I valori supportati sono: `v1`, `v2`. | 1.5.2
`<subscriptionId>` | false | Specificare l'ID sottoscrizione. | 0.1.0+
`<resourceGroup>` | true | Azure <abbr title="Un contenitore logico per le risorse di Azure correlate che è possibile gestire come unità.">gruppo di risorse</abbr> per l'app Web. | 0.1.0+
`<appName>` | true | Nome dell'App Web. | 0.1.0+
`<region>` | true | Specifica l'area in cui verrà ospitata l'app Web. Il valore predefinito è **westeurope**. Le aree valide sono disponibili nella sezione [Aree supportate](https://azure.microsoft.com/global-infrastructure/services/?products=app-service). | 0.1.0+
`<pricingTier>` | false | Il piano tariffario per l'App Web. Il valore predefinito è **P1V2** per il carico di lavoro di produzione, mentre **B2** è il valore minimo consigliato per le attività di sviluppo/test Java. [Scopri di più](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | Configurazione dell'ambiente di runtime. I dettagli sono disponibili [qui](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | true | Configurazione della distribuzione. I dettagli sono disponibili [qui](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Prestare attenzione ai valori di `<appName>` e `<resourceGroup>` ( `helloworld-1590394316693` e `helloworld-1590394316693-rg` di conseguenza nell'output di esempio) verranno utilizzati in un secondo momento.

[Segnalare un problema](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. distribuire l'app

Distribuire l'app Java in Azure usando il comando seguente.

```bash
mvn package azure-webapp:deploy
```

Al termine della distribuzione, l'applicazione sarà pronta all'indirizzo `http://<appName>.azurewebsites.net/` . Aprire l'URL con il browser Web locale. Dovrebbe essere visualizzata la schermata seguente:

![App di esempio in esecuzione nel Servizio app di Azure](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Congratulazioni** La distribuzione della prima app Java nel servizio app è stata completata.

[Segnalare un problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. pulire le risorse

Eliminare l'app Java e le relative risorse per evitare di incorrere in costi aggiuntivi.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

L'esecuzione del comando può richiedere un minuto.

<br>
<hr/>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi al database SQL di Azure per PostgreSQL con Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Configurare CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informazioni sui prezzi](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Aggregare log e metriche](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Aumentare le prestazioni](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Risorse Azure per sviluppatori Java](/java/azure/)

> [!div class="nextstepaction"]
> [Configurare l'app Java](configure-language-java.md)
