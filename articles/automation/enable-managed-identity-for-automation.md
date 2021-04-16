---
title: Abilitare un'identità gestita per l'account Automazione di Azure (anteprima)
description: Questo articolo descrive come configurare l'identità gestita per gli Automazione di Azure account.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514802"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Abilitare un'identità gestita per l'account Automazione di Azure (anteprima)

Questo argomento illustra come creare un'identità gestita per un account Automazione di Azure e come usarla per accedere ad altre risorse. Per altre informazioni sul funzionamento dell'identità gestita con Automazione di Azure, vedere [Identità gestite](automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Prerequisiti

- Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Sia l'identità gestita che le risorse di Azure di destinazione gestite dal runbook usando tale identità devono essere nella stessa sottoscrizione di Azure.

- La versione più recente dei moduli Automazione di Azure account. Attualmente è 1.6.0. Per informazioni [dettagliate su questa versione, vedere Az.Automation 1.6.0.](https://www.powershellgallery.com/packages/Az.Automation/1.6.0)

- Una risorsa di Azure a cui si vuole accedere dal runbook di Automazione. Questa risorsa deve avere un ruolo definito per l'identità gestita, che consente al runbook di Automazione di autenticare l'accesso alla risorsa. Per aggiungere ruoli, è necessario essere un proprietario per la risorsa nel tenant di Azure AD corrispondente.

- Se si vogliono eseguire processi ibridi usando un'identità gestita, aggiornare il ruolo di lavoro ibrido per runbook alla versione più recente. Le versioni minime richieste sono:

   - Ruolo di lavoro ibrido per runbook di Windows: versione 7.3.1125.0
   - Ruolo di lavoro ibrido per runbook Linux: versione 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Abilitare l'identità assegnata dal sistema

>[!IMPORTANT]
>La nuova identità a livello di account di Automazione eseguirà l'override di tutte le identità assegnate dal sistema a livello di macchina virtuale precedenti ( descritte in Usare l'autenticazione [del runbook con identità gestite](/automation-hrw-run-runbooks#runbook-auth-managed-identities)). Se si eseguono processi ibridi in macchine virtuali di Azure che usano l'identità assegnata dal sistema di una macchina virtuale per accedere alle risorse del runbook, l'identità dell'account di Automazione verrà usata per i processi ibridi. Ciò significa che l'esecuzione del processo esistente potrebbe essere interessata se si usa la funzionalità Customer Managed Keys (CMK) dell'account di Automazione.<br/><br/>Se si vuole continuare a usare l'identità gestita della macchina virtuale, non è consigliabile abilitare l'identità a livello di account di Automazione. Se è già stata abilitata, è possibile disabilitare l'identità gestita dell'account di Automazione. Vedere [Disabilitare l'Automazione di Azure gestita dell'account utente.](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation)

La configurazione delle identità assegnate dal sistema per Automazione di Azure può essere eseguita in uno dei due modi seguenti. È possibile usare il portale di Azure o l'API REST di Azure.

>[!NOTE]
>Le identità assegnate dall'utente non sono ancora supportate.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Abilitare l'identità assegnata dal sistema nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare all'account di Automazione e selezionare **Identità in** **Impostazioni account.**

1. Impostare **l'opzione Assegnata** dal sistema **su On e** premere **Salva**. Quando viene richiesto di confermare, selezionare **Sì.**

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Abilitazione dell'identità assegnata dal sistema in portale di Azure.":::

L'account di Automazione può ora usare l'identità assegnata dal sistema, registrata con Azure Active Directory (Azure AD) ed è rappresentata da un ID oggetto.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="ID oggetto identità gestita.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Abilitare l'identità assegnata dal sistema tramite l'API REST

È possibile configurare un'identità gestita assegnata dal sistema per l'account di Automazione usando la chiamata API REST seguente.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Testo della richiesta
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Proprietà (JSON) | valore | Descrizione|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Identificatore univoco globale (GUID) dell'oggetto entità servizio per l'identità gestita che rappresenta l'account di Automazione nel tenant Azure AD servizio. Questo GUID viene talvolta visualizzato come "ID oggetto" o OBJECTID. |
| tenantid | \<Azure-AD-tenant-ID\> | Identificatore univoco globale (GUID) che rappresenta il tenant Azure AD cui l'account di Automazione è ora membro. All'interno Azure AD tenant, l'entità servizio ha lo stesso nome dell'account di Automazione. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Concedere all'identità l'accesso alle risorse di Azure ottenendo un token

Un account di Automazione può usare l'identità gestita per ottenere i token per accedere ad altre risorse protette da Azure AD, ad esempio Azure Key Vault. Questi token non rappresentano alcun utente specifico dell'applicazione. Rappresentano invece l'applicazione che accede alla risorsa. In questo caso, ad esempio, il token rappresenta un account di Automazione.

Prima di poter usare l'identità gestita assegnata dal sistema per l'autenticazione, configurare l'accesso per tale identità nella risorsa di Azure in cui si prevede di usare l'identità. Per completare questa attività, assegnare il ruolo appropriato all'identità nella risorsa di destinazione di Azure.

Questo esempio usa Azure PowerShell per illustrare come assegnare il ruolo Collaboratore nella sottoscrizione alla risorsa di Azure di destinazione. Il ruolo Collaboratore viene usato come esempio e può essere richiesto o meno nel caso specifico.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Eseguire l'autenticazione dell'accesso con l'identità gestita

Dopo aver abilitato l'identità gestita per l'account di Automazione e aver dato a un'identità l'accesso alla risorsa di destinazione, è possibile specificare tale identità nei runbook per le risorse che supportano l'identità gestita. Per il supporto delle identità, usare il `Connect-AzAccount` cmdlet Az. Vedere [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) nelle informazioni di riferimento su PowerShell.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Se l'organizzazione usa ancora i cmdlet deprecati di AzureRM, è possibile usare `Connect-AzureRMAccount -Identity` .

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Generare un token di accesso senza usare i cmdlet di Azure

Per gli endpoint HTTP assicurarsi di quanto segue.
- L'intestazione dei metadati deve essere presente e deve essere impostata su "true".
- Una risorsa deve essere passata insieme alla richiesta, come parametro di query per una richiesta GET e come dati del modulo per una richiesta POST.
- X-IDENTITY-HEADER deve essere impostato sul valore della variabile di ambiente IDENTITY_HEADER per i dipendenti ibridi per runbook. 
- Il tipo di contenuto per la richiesta Post deve essere 'application/x-www-form-urlencoded'. 

### <a name="sample-get-request"></a>Richiesta GET di esempio

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Richiesta POST di esempio
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Runbook di esempio con identità gestita

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Runbook di esempio per accedere a un database SQL senza usare i cmdlet di Azure

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Runbook di esempio per accedere a un insieme di credenziali delle chiavi usando i cmdlet di Azure

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Runbook Python di esempio per ottenere un token
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Passaggi successivi

- Se è necessario disabilitare un'identità gestita, vedere [Disabilitare l Automazione di Azure'identità gestita dell'account utente (anteprima).](disable-managed-identity-for-automation.md)

- Per una panoramica della sicurezza degli account Automazione di Azure, vedere Panoramica [dell'autenticazione dell'account di Automazione.](automation-security-overview.md)