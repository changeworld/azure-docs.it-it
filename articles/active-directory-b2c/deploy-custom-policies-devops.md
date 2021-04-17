---
title: Distribuire criteri personalizzati con Azure Pipelines
titleSuffix: Azure AD B2C
description: Informazioni su come distribuire Azure AD B2C criteri personalizzati in una pipeline CI/CD usando Azure Pipelines in Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529434"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Distribuire criteri personalizzati con Azure Pipelines

Usando una pipeline di integrazione e recapito continuo (CI/CD) impostata in [Azure Pipelines][devops-pipelines], è possibile includere i criteri personalizzati di Azure AD B2C nella distribuzione del software e nell'automazione del controllo del codice. Durante la distribuzione in ambienti di Azure AD B2C diversi, ad esempio sviluppo, test e produzione, è consigliabile rimuovere i processi manuali ed eseguire test automatizzati usando Azure Pipelines.

Sono necessari tre passaggi principali per abilitare Azure Pipelines gestire i criteri personalizzati all'interno Azure AD B2C:

1. Creare una registrazione dell'applicazione Web nel tenant Azure AD B2C
1. Configurare un repo di Azure
1. Configurare una pipeline di Azure

> [!IMPORTANT]
> La gestione Azure AD B2C criteri personalizzati con  una pipeline di Azure usa attualmente le operazioni di anteprima disponibili nell'endpoint `/beta` Microsoft Graph'API. L'uso di queste API nelle applicazioni di produzione non è supportato. Per altre informazioni, vedere le informazioni di riferimento [Microsoft Graph endpoint beta dell'API REST.](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)

## <a name="prerequisites"></a>Prerequisiti

* [Azure AD B2C tenant](tutorial-create-tenant.md)e le credenziali per un utente nella directory con il ruolo di amministratore dei criteri [IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Criteri personalizzati](tutorial-create-user-flows.md?pivots=b2c-custom-policy) caricati nel tenant
* [App di gestione](microsoft-graph-get-started.md) registrata nel tenant con l'autorizzazione Microsoft Graph'API *Policy.ReadWrite.TrustFramework*
* [Azure Pipeline e](https://azure.microsoft.com/services/devops/pipelines/)accesso a un [progetto Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Flusso di concessione di credenziali client

Lo scenario descritto qui usa le chiamate da servizio a servizio tra Azure Pipelines e Azure AD B2C usando il flusso di concessione delle credenziali [client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)OAuth 2.0. Questo flusso di concessione consente a un servizio Web come Azure Pipelines (client riservato) di usare le proprie credenziali invece di rappresentare un utente per l'autenticazione quando si chiama un altro servizio Web (in questo caso l'API Microsoft Graph). Azure Pipelines ottiene un token in modo non interattivo, quindi effettua richieste all'API Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Registrare un'applicazione per le attività di gestione

Come accennato in [Prerequisiti,](#prerequisites)è necessaria una registrazione dell'applicazione che gli script di PowerShell, eseguiti da Azure Pipelines, possono usare per accedere alle risorse nel tenant.

Se **si** ha già una registrazione dell'applicazione che si usa per le attività di automazione, assicurarsi che sia stata concessa l'autorizzazione  >    >  **policy.ReadWrite.TrustFramework di Microsoft Graph Policy.ReadWrite.TrustFramework** nelle autorizzazioni **API** della registrazione dell'app.

Per istruzioni sulla registrazione di un'applicazione di gestione, vedere [Gestire Azure AD B2C con Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Configurare un riposino di Azure

Con un'applicazione di gestione registrata, è possibile configurare un repository per i file dei criteri.

1. Accedere all'organizzazione Azure DevOps Services locale.
1. [Creare un nuovo progetto][devops-create-project] o selezionarne uno esistente.
1. Nel progetto passare a **Repository e** selezionare la **pagina** File. Selezionare un repository esistente o crearne uno per questo esercizio.
1. Creare una cartella denominata *B2CAssets*. Assegnare al file segnaposto *README.md* il nome **Commit** del file. È possibile rimuovere questo file in un secondo momento, se si desidera.
1. Aggiungere i Azure AD B2C file di criteri alla *cartella B2CAssets.* Sono inclusi *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* e tutti gli altri criteri creati. Registrare il nome file di Azure AD B2C file di criteri da usare in un passaggio successivo (vengono usati come argomenti di script di PowerShell).
1. Creare una cartella denominata *Scripts* nella directory radice del repository e assegnare al file segnaposto il nome *DeployToB2c.ps1*. Non eseguire il commit del file a questo punto, ma in un passaggio successivo.
1. Incollare lo script di PowerShell *seguenteDeployToB2c.ps1* e quindi eseguire **il commit** del file. Lo script acquisisce un token da Azure AD e chiama l'API Microsoft Graph per caricare i criteri all'interno della cartella *B2CAssets* nel tenant Azure AD B2C.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Configurare la pipeline di Azure

Con il repository inizializzato e popolato con i file di criteri personalizzati, si è pronti per configurare la pipeline di versione.

### <a name="create-pipeline"></a>Creare una pipeline

1. Accedere all'organizzazione Azure DevOps Services e passare al progetto.
1. Nel progetto selezionare **Pipelines**  >  Releases New pipeline (Versioni **pipeline**  >  **nuova pipeline).**
1. In **Selezionare un modello** selezionare Processo **vuoto.**
1. Immettere un **nome di fase,** ad *esempio DeployCustomPolicies,* quindi chiudere il riquadro.
1. Selezionare **Aggiungi un artefatto** e in **Tipo di origine** selezionare Repository **di Azure.**
    1. Scegliere il repository di origine contenente *la cartella* Scripts popolata con lo script di PowerShell.
    1. Scegliere un **ramo predefinito.** Se è stato creato un nuovo repository nella sezione precedente, il ramo predefinito è *master*.
    1. Lasciare **l'impostazione Versione** predefinita *di Più recente dal ramo predefinito*.
    1. Immettere un **alias di origine** per il repository. Ad esempio, *policyRepo*. Non includere spazi nel nome alias.
1. Selezionare **Aggiungi**
1. Rinominare la pipeline in modo che rifletta la finalità. Ad esempio, *Distribuire una pipeline di criteri personalizzati.*
1. Selezionare **Salva** per salvare la configurazione della pipeline.

### <a name="configure-pipeline-variables"></a>Configurare le variabili della pipeline

1. Selezionare la **scheda** Variabili.
1. Aggiungere le variabili seguenti in **Variabili pipeline e** impostarne i valori come specificato:

    | Nome | Valore |
    | ---- | ----- |
    | `clientId` | **ID applicazione (client)** dell'applicazione registrata in precedenza. |
    | `clientSecret` | Valore del segreto **client creato** in precedenza. <br /> Modificare il tipo di variabile in **segreto** (selezionare l'icona a forma di lucchetto). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, dove *your-b2c-tenant* è il nome del tenant Azure AD B2C tenant. |

1. Selezionare **Salva** per salvare le variabili.

### <a name="add-pipeline-tasks"></a>Aggiungere attività della pipeline

Aggiungere quindi un'attività per distribuire un file di criteri.

1. Selezionare la **scheda** Attività.
1. Selezionare **Processo di Agent** e quindi selezionare il segno più ( ) per aggiungere **+** un'attività al processo di Agent.
1. Cercare e selezionare **PowerShell.** Non selezionare "Azure PowerShell", "PowerShell nei computer di destinazione" o un'altra voce di PowerShell.
1. Selezionare l'attività **Script di PowerShell appena** aggiunta.
1. Immettere i valori seguenti per l'attività Script di PowerShell:
    * **Versione attività:** 2.*
    * **Nome visualizzato:** nome dei criteri che l'attività deve caricare. Ad esempio, *B2C_1A_TrustFrameworkBase*.
    * **Tipo:** Percorso file
    * **Percorso script:** selezionare i puntini di sospensione (**_..._* _), passare alla cartella _Scripts* e quindi selezionare il file *DeployToB2C.ps1* file.
    * **Argomenti:**

        Immettere i valori seguenti per **Argomenti**. Sostituire `{alias-name}` con l'alias specificato nella sezione precedente.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Ad esempio, se l'alias specificato è *policyRepo*, la riga dell'argomento deve essere:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selezionare **Salva** per salvare il processo dell'agente.

L'attività appena aggiunta carica *un* file di criteri Azure AD B2C. Prima di procedere, attivare manualmente il processo (**Crea versione**) per assicurarsi che venga completato correttamente prima di creare attività aggiuntive.

Se l'attività viene completata correttamente, aggiungere attività di distribuzione eseguendo i passaggi precedenti per ognuno dei file di criteri personalizzati. Modificare i `-PolicyId` valori degli argomenti e per ogni `-PathToFile` criterio.

è `PolicyId` un valore trovato all'inizio di un file di criteri XML all'interno del nodo TrustFrameworkPolicy. Ad esempio, nel codice XML dei criteri seguente `PolicyId` viene B2C_1A_TrustFrameworkBase : 

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Quando si eseguono gli agenti e si caricano i file dei criteri, assicurarsi che siano caricati nell'ordine seguente:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Il Identity Experience Framework applica questo ordine poiché la struttura di file è compilata su una catena gerarchica.

## <a name="test-your-pipeline"></a>Testare la pipeline

Per testare la pipeline di versione:

1. Selezionare **Pipeline e** quindi **Versioni**.
1. Selezionare la pipeline creata in precedenza, ad *esempio DeployCustomPolicies*.
1. Selezionare **Crea versione** e quindi Crea **per** accodare la versione.

Verrà visualizzato un banner di notifica che indica che una versione è stata accodata. Per visualizzarne lo stato, selezionare il collegamento nel banner di notifica o selezionarlo nell'elenco nella **scheda** Versioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

* [Chiamate da servizio a servizio con credenziali client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
