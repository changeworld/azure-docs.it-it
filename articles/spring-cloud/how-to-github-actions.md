---
title: Azure Spring Cloud CI/CD con GitHub Actions
description: Come creare un flusso di lavoro CI/CD per Azure Spring Cloud con GitHub Actions
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c52279108a8fd8d5a7ac8bbd7c8eb215097b21b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791356"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD con GitHub Actions

GitHub Actions un flusso di lavoro automatizzato del ciclo di vita di sviluppo software. Con GitHub Actions per Azure Spring Cloud è possibile creare flussi di lavoro nel repository per compilare, testare, creare pacchetti, rilasciare e distribuire in Azure. 

## <a name="prerequisites"></a>Prerequisiti
Questo esempio richiede l'interfaccia della riga [di comando di Azure](/cli/azure/install-azure-cli).

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Configurare il repository GitHub ed eseguire l'autenticazione
È necessaria una credenziale dell'entità servizio di Azure per autorizzare l'azione di accesso ad Azure. Per ottenere le credenziali di Azure, eseguire i comandi seguenti nel computer locale:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Per accedere a un gruppo di risorse specifico, è possibile ridurre l'ambito:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Il comando deve eseguire l'output di un oggetto JSON:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Questo esempio usa [l'esempio steeltoe in GitHub.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)  Creare una fork del repository, aprire la pagina del repository GitHub per il fork e selezionare la **scheda** Impostazioni. Aprire il menu **Segreti** e selezionare **Nuovo segreto:**

 ![Aggiungere un nuovo segreto](./media/github-actions/actions1.png)

Impostare il nome del segreto su e il relativo valore sulla stringa JSON presente nell'intestazione Configurare il `AZURE_CREDENTIALS` *repository GitHub ed eseguire l'autenticazione di*.

 ![Impostare i dati dei segreti](./media/github-actions/actions2.png)

È anche possibile ottenere le credenziali di accesso di Azure da Key Vault in GitHub Actions, come illustrato in [Autenticare Azure Spring con Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Effettuare il provisioning dell'istanza del servizio
Per effettuare il provisioning dell'istanza Azure Spring Cloud servizio, eseguire i comandi seguenti usando l'interfaccia della riga di comando di Azure.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Compilare il flusso di lavoro
Il flusso di lavoro viene definito usando le opzioni seguenti.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparare la distribuzione con l'interfaccia della riga di comando di Azure

Il comando `az spring-cloud app create` non è attualmente idempotente. Dopo l'esecuzione, verrà visualizzato un errore se si esegue di nuovo lo stesso comando. È consigliabile usare questo flusso di lavoro per le Azure Spring Cloud e le istanze esistenti.

Usare i comandi seguenti dell'interfaccia della riga di comando di Azure per la preparazione:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuire direttamente con l'interfaccia della riga di comando di Azure

Creare il `.github/workflows/main.yml` file nel repository con il contenuto seguente. Sostituire `<your resource group name>` e con i valori `<your service name>` corretti.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>Configurare il repository GitHub ed eseguire l'autenticazione
È necessaria una credenziale dell'entità servizio di Azure per autorizzare l'azione di accesso di Azure. Per ottenere le credenziali di Azure, eseguire i comandi seguenti nel computer locale:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Per accedere a un gruppo di risorse specifico, è possibile ridurre l'ambito:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Il comando deve eseguire l'output di un oggetto JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Questo esempio usa [l'esempio PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) in GitHub.  Creare un fork dell'esempio, aprire la pagina del repository GitHub e fare clic **sulla scheda** Impostazioni. Aprire il menu **Segreti** e fare clic su Aggiungi un **nuovo segreto:**

 ![Aggiungere un nuovo segreto](./media/github-actions/actions1.png)

Impostare il nome del segreto su e il relativo valore sulla stringa JSON trovata sotto l'intestazione Configurare il `AZURE_CREDENTIALS` *repository GitHub ed eseguire l'autenticazione.*

 ![Impostare i dati dei segreti](./media/github-actions/actions2.png)

È anche possibile ottenere le credenziali di accesso di Azure Key Vault nelle azioni di GitHub, come illustrato [in Autenticare Azure Spring con Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Effettuare il provisioning dell'istanza del servizio
Per effettuare il provisioning dell'Azure Spring Cloud del servizio, eseguire i comandi seguenti usando l'interfaccia della riga di comando di Azure.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Compilare il flusso di lavoro
Il flusso di lavoro viene definito usando le opzioni seguenti.

### <a name="prepare-for-deployment-with-azure-cli"></a>Preparare la distribuzione con l'interfaccia della riga di comando di Azure
Il comando `az spring-cloud app create` non è attualmente idempotente.  È consigliabile usare questo flusso di lavoro per le Azure Spring Cloud e le istanze esistenti.

Usare i comandi seguenti dell'interfaccia della riga di comando di Azure per la preparazione:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Distribuire direttamente con l'interfaccia della riga di comando di Azure
Creare il `.github/workflow/main.yml` file nel repository:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Eseguire la distribuzione con l'azione dell'interfaccia della riga di comando di Azure
Il comando az `run` userà la versione più recente dell'interfaccia della riga di comando di Azure. In caso di modifiche di rilievo, è anche possibile usare una versione specifica dell'interfaccia della riga di comando di Azure con `action` azure/CLI. 

> [!Note] 
> Questo comando verrà eseguito in un nuovo contenitore, quindi non funzionerà e l'accesso ai file tra azioni `env` potrebbe avere restrizioni aggiuntive.

Creare il file .github/workflow/main.yml nel repository:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Distribuire con il plug-in Maven
Un'altra opzione è usare il [plug-in Maven](./spring-cloud-quickstart.md) per distribuire il file Jar e aggiornare le impostazioni dell'app. Il comando `mvn azure-spring-cloud:deploy` è idempotente e creerà automaticamente le app, se necessario. Non è necessario creare le app corrispondenti in anticipo.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>Eseguire il flusso di lavoro

GitHub **Actions deve** essere abilitato automaticamente dopo il push in `.github/workflow/main.yml` GitHub. L'azione verrà attivata quando si esegue il push di un nuovo commit. Se si crea questo file nel browser, l'azione dovrebbe essere già stata eseguita.

Per verificare che l'azione sia stata abilitata, fare clic **sulla scheda Azioni** nella pagina del repository GitHub:

![Verificare che l'azione sia abilitata](./media/github-actions/actions3.png)

Se l'azione viene eseguita in modo non corretto, ad esempio se le credenziali di Azure non sono state impostate, è possibile eseguire nuovamente i controlli dopo aver corretto l'errore. Nella pagina del repository GitHub fare clic su **Azioni,** selezionare l'attività del flusso di lavoro specifica e quindi fare clic sul pulsante Riesegui **controlli** per eseguire di nuovo i controlli:

![Eseguire di nuovo i controlli](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Passaggi successivi

* [Key Vault per Spring Cloud github](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory di servizio](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [GitHub Actions per Azure](https://github.com/Azure/actions/)
