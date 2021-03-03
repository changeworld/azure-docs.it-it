---
title: Classifica i dati SQL di Azure usando le etichette di competenza di Azure
description: Importare la classificazione dall'ambito Azure nel database SQL di Azure e Azure Synpase Analytics
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714902"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Classifica i dati SQL di Azure usando le etichette di competenza di Azure
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

Questo documento descrive come aggiungere etichette di competenza di Azure nel database SQL di Azure e in Azure sinapsi Analytics (in precedenza SQL DW).

## <a name="create-an-application"></a>Creare un'applicazione

1. Dal portale di Azure aprire il **Azure Active Directory**.
2. In **Gestisci** selezionare **registrazione app**.
3. Creare una nuova app Azure Active Directory selezionando **nuova applicazione**.
4. Immettere un nome per l'applicazione e selezionare **Register (registra**).
5. Dopo aver creato l'applicazione, aprire **certificati & segreti** in **gestione**.
6. Creare un nuovo segreto client selezionando **nuovo segreto client** in **segreti client**.
7. Aggiungere una descrizione al segreto client, selezionare un periodo di scadenza e selezionare **Aggiungi**.
8. Mantieni il **valore** per un uso futuro.

   > [!NOTE]
   > Una volta chiusa la pagina, il valore verrà mascherato. Se si torna alla pagina, non sarà possibile recuperare il valore del segreto client. Sarà necessario generare un nuovo segreto client.

9. Tornare alla pagina Panoramica dell'applicazione appena creata e copiare i valori seguenti per un uso futuro:
    1. ID applicazione (client)
    1. ID directory (tenant)

## <a name="provide-permissions-to-the-application"></a>Fornire le autorizzazioni per l'applicazione

1. Nella portale di Azure cercare gli account di **competenza**.
2. Selezionare l'account di competenza in cui vengono classificati i database e le sinapsi SQL.
3. Aprire **controllo di accesso (IAM)** e selezionare **Aggiungi**.

4. Selezionare **Aggiungi un'assegnazione di ruolo**.
5. Nella sezione **Role** cercare **lettore dati di competenza** e selezionarlo.
6. Nella sezione **Seleziona** cercare l'applicazione creata in precedenza, selezionarla e fare clic su **Salva**.

## <a name="extract-the-classification-from-azure-purview"></a>Estrai la classificazione da Azure competenza

1. Aprire l'account di competenza e, nella Home page, cercare il database SQL di Azure o l'analisi delle sinapsi di Azure in cui si vogliono copiare le etichette.
2. Copiare il QualifiedName in **Proprietà** e mantenerlo per un uso futuro.
3. Aprire la shell di PowerShell.

4. Copiare uno degli script seguenti in base al tipo di asset SQL (database SQL di Azure o sinapsi di Azure).
5. Compilare i parametri con i valori copiati in precedenza:

   a. $TenantID: sezione 1, passaggio 9
   
   b. $ClientID: sezione 1, passaggio 9
   
   c. $SecretID: sezione 1, passaggio 8
   
   d. $purviewAccountName: sezione 2, passaggio 2
   
   e. $sqlDatabaseName: sezione 3, passaggio 2

6. Copiare l'output dello script per un uso futuro.

### <a name="for-azure-sql-database"></a>Per il database SQL di Azure

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Per Azure sinapsi Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Eseguire il comando T-SQL sull'asset SQL

1. Connettersi al database SQL di Azure o alla sinapsi di Azure usando lo strumento scelto.
2. Eseguire il comando T-SQL copiato dalla sezione precedente.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Per altre informazioni su Azure, vedere la [documentazione di Azure](../../purview/index.yml)per le competenze.