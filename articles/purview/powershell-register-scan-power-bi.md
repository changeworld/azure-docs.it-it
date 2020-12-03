---
title: Usare PowerShell per registrare e analizzare Power BI (anteprima)
description: Informazioni su come usare PowerShell per registrare e analizzare un tenant di Power BI in Azure.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552428"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Usare PowerShell per registrare e analizzare Power BI in Azure (anteprima) 

Questo articolo illustra come usare PowerShell per configurare un'analisi di un tenant di Power BI in un catalogo di Azure per le competenze.

## <a name="power-bi-authentication-background"></a>Sfondo dell'autenticazione Power BI

Il catalogo di competenza deve connettersi all'API di amministrazione Power BI per analizzare gli artefatti in un tenant di Power BI. L'API di amministrazione Power BI attualmente supporta due tipi di autenticazione:

- Identità gestita (MSI).
- Autenticazione utente delegata.

> [!Note]
> L'identità del servizio gestito è consigliata, a meno che non sia necessaria l'autenticazione dell'utente delegato.

## <a name="create-a-security-group"></a>Creare un gruppo di sicurezza

Ogni catalogo di competenze ha una propria identità gestita assegnata dal sistema a cui deve essere concesso l'accesso al tenant di Power BI per abilitare l'analisi. Il nome del catalogo può essere usato per trovare la propria identità in portale di Azure.

1. Nella [portale di Azure](https://portal.azure.com)cercare Azure Active Directory.
1. Creare un nuovo gruppo di sicurezza nel Azure Active Directory, seguendo la procedura [creare un gruppo di base e aggiungere membri utilizzando Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > È possibile ignorare questo passaggio se si dispone già di un gruppo di sicurezza da usare.

1. Assicurarsi di selezionare sicurezza come tipo di **gruppo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo di gruppo di sicurezza":::

1. Aggiungere l'identità gestita del catalogo a questo gruppo di sicurezza selezionando membri, quindi **+ Aggiungi membri**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Aggiungere l'istanza gestita del catalogo al gruppo":::

1. Cercare il catalogo e selezionarlo.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Aggiungi catalogo eseguendo una ricerca":::

1. Verrà visualizzata una notifica di esito positivo che mostra che è stata aggiunta.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Aggiunta del file MSI del catalogo completata":::

## <a name="associate-the-security-group-with-power-bi"></a>Associare il gruppo di sicurezza a Power BI

1. Accedere al [portale di amministrazione Power bi](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Aggiungere questo flag di funzionalità:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Questo flag Abilita la funzionalità che consente di associare il gruppo di sicurezza. ad esempio:

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Per visualizzare la pagina delle impostazioni del tenant, è necessario essere un amministratore Power BI.

1. Selezionare **le impostazioni per gli sviluppatori**  >  **Consenti alle entità servizio di usare le API Power BI di sola lettura (anteprima)**.
1. Selezionare **gruppi di sicurezza specifici**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Immagine che illustra come consentire alle entità servizio di ottenere le autorizzazioni di sola lettura Power BI API di amministrazione ":::

    > [!Caution]
    > Quando si consente al gruppo di sicurezza creato (con l'identità gestita del catalogo dati come membro) di usare le API di amministrazione di Power BI di sola lettura, si consente anche di accedere ai metadati (ad esempio, i nomi di dashboard e report, i proprietari, le descrizioni e così via) per tutti gli elementi di Power BI in questo tenant. Una volta che i metadati sono stati inseriti nell'ambito di Azure, le autorizzazioni di competenza, non Power BI autorizzazioni, determinano chi può visualizzare tali metadati.

    > [!Note]
    > È possibile rimuovere il gruppo di sicurezza dalle impostazioni dello sviluppatore, ma i metadati estratti in precedenza non verranno rimossi dall'account di competenza. È possibile eliminarlo separatamente.

## <a name="register-power-bi-and-set-up-a-scan"></a>Registrare Power BI e configurare un'analisi

Ora che sono state concesse le autorizzazioni per il catalogo per connettersi all'API di amministrazione del tenant di Power BI, è necessario configurare l'analisi nel catalogo. A tale scopo, è necessario configurare ed eseguire uno script di PowerShell.

1. Scaricare ed estrarre i cmdlet di PowerShell per ADC.
1. Configurare lo script specificando i valori per le assegnazioni all'inizio dello script.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > È necessario essere un collaboratore o un proprietario nella sottoscrizione in cui vengono eseguiti i comandi.

1. Avviare l'analisi eseguendo lo script seguente:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Registra e analizza Power BI

Il metodo di autenticazione consigliato è MSI. Tuttavia, per eseguire la scansione di un tenant di Power BI che si trova in un tenant di Azure diverso rispetto al catalogo, usare l'autenticazione delegata.

Per eseguire l'autenticazione delegata, è necessario disporre delle credenziali utente amministratore, nonché Power BI le credenziali di amministratore. È anche necessario creare un'app di Azure e concederla Power BI autorizzazioni tenant. ReadAll.

1. Passare a [portale di Azure](https://portal.azure.com) e cercare registrazioni per l' **app**.

1. Da **registrazioni app** selezionare **+ nuova registrazione**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Immagine che illustra come creare una nuova registrazione di app di Azure":::

1. Immettere un nome per l'app.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="registrare una nuova app":::

1. Una volta creata l'app, selezionare **autorizzazioni API** e quindi **+ Aggiungi un'autorizzazione**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Immagine che illustra come aggiungere l'autorizzazione all'app":::

1. Selezionare **Power bi servizio** sulle **autorizzazioni dell'API richiesta**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Immagine che Mostra come selezionare il servizio PBI":::

1. Selezionare **autorizzazioni delegate** e **tenant. Read. All**. Selezionare quindi **Aggiungi autorizzazioni**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Immagine che illustra come richiedere le autorizzazioni API":::

1. Selezionare **Concedi consenso amministratore**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Immagine che Mostra come concedere il consenso dell'amministratore":::

1. Copiare l' **ID applicazione (client)** e i valori **ID directory (tenant)** .  Questi valori verranno usati quando si configura l'analisi.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Immagine che mostra la copia degli ID client e tenant":::

1. Configurare l'analisi in PowerShell. Lo script richiederà le credenziali. Per la sottoscrizione di Azure che si intende usare è necessario disporre almeno del ruolo Collaboratore e amministratore dell'origine dati di competenza.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Eseguire l'analisi.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure, vedere [Guida introduttiva: creare un account Azure per la competenza](create-catalog-portal.md).
