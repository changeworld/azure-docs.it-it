---
title: Accesso granulare in base al ruolo Azure HDInsight cluster
description: Informazioni sulle modifiche necessarie come parte della migrazione all'accesso granulare in base al ruolo per le configurazioni del cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: afb30f4648f1649bf6cc6cc6a3bf02f433f49d45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774932"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Eseguire la migrazione all'accesso granulare in base al ruolo per le configurazioni dei cluster

Sono state introdotte alcune importanti modifiche per supportare un accesso in base al ruolo più granulare per ottenere informazioni riservate. Come parte di queste modifiche, potrebbe essere necessaria un'azione entro il 3 settembre **2019** se si usa una delle [entità o degli scenari interessati.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Cosa cambierà

In precedenza, i segreti potevano essere ottenuti tramite l'API HDInsight dagli utenti del cluster che avevano i ruoli di [Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)Proprietario, Collaboratore o Lettore, perché erano disponibili per chiunque abbia `*/read` l'autorizzazione. I segreti sono definiti come valori che possono essere usati per ottenere un accesso con privilegi più elevati di quelli consentiti dal ruolo di un utente. Sono inclusi valori come le credenziali HTTP del gateway del cluster, le chiavi dell'account di archiviazione e le credenziali del database.

A partire dal 3 settembre 2019, l'accesso a questi segreti richiederà l'autorizzazione , ovvero gli utenti con il ruolo Lettore non potranno più `Microsoft.HDInsight/clusters/configurations/action` accedervi. I ruoli che dispongono di questa autorizzazione sono Collaboratore, Proprietario e il nuovo ruolo Operatore cluster HDInsight (altre informazioni di seguito).

Verrà anche introdotto un nuovo ruolo Operatore cluster [HDInsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) che sarà in grado di recuperare i segreti senza che vengano concesse le autorizzazioni amministrative di Collaboratore o Proprietario. Per concludere:

| Ruolo                                  | In precedenza                                                                                        | In futuro       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Reader                                | - Accesso in lettura, inclusi i segreti.                                                                   | - Accesso in lettura, **esclusi i** segreti | 
| Operatore di cluster HDInsight<br>(Nuovo ruolo) | N/D                                                                                              | - Accesso in lettura/scrittura, inclusi i segreti         | 
| Collaboratore                           | - Accesso in lettura/scrittura, inclusi i segreti.<br>- Creare e gestire tutti i tipi di risorse di Azure.<br>: consente di eseguire azioni script.     | Nessuna modifica |
| Proprietario                                 | - Accesso in lettura/scrittura, inclusi i segreti.<br>- Accesso completo a tutte le risorse<br>- Delegare l'accesso ad altri utenti.<br>- Eseguire azioni script. | Nessuna modifica |

Per informazioni su come aggiungere l'assegnazione di ruolo Operatore cluster HDInsight a un utente per concedere l'accesso in lettura/scrittura ai segreti del cluster, vedere la sezione seguente Aggiungere l'assegnazione di ruolo Operatore cluster [HDInsight a un utente](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sono interessati da queste modifiche?

Sono interessati le entità e gli scenari seguenti:

- [API:](#api)utenti che usano `/configurations` gli endpoint o `/configurations/{configurationName}` .
- [Azure HDInsight Tools per Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versione 1.1.1 o successiva.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) versione 3.20.0 o successiva.
- [Strumenti di Analisi di](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) flusso e Azure Data Lake Visual Studio versione 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) versione 3.15.0 o successiva.
- [SDK per .NET](#sdk-for-net)
    - [versioni 1.x o 2.x:](#versions-1x-and-2x)utenti che usano i metodi , , o della classe `GetClusterConfigurations` `GetConnectivitySettings` `ConfigureHttpSettings` `EnableHttp` `DisableHttp` ConfigurationsOperationsExtensions.
    - [versioni 3.x e successive:](#versions-3x-and-up)utenti che usano i metodi `Get` , , o della classe `Update` `EnableHttp` `DisableHttp` `ConfigurationsOperationsExtensions` .
- [SDK per Python:](#sdk-for-python)utenti che usano `get` i metodi o della classe `update` `ConfigurationsOperations` .
- [SDK per Java:](#sdk-for-java)utenti che usano `update` i metodi o della classe `get` `ConfigurationsInner` .
- [SDK per Go:](#sdk-for-go)utenti che usano `Get` i metodi o dello `Update` `ConfigurationsClient` struct.
- [Az.HDInsight PowerShell versione](#azhdinsight-powershell) 2.0.0.
Vedere le sezioni seguenti (o usare i collegamenti precedenti) per visualizzare i passaggi di migrazione per lo scenario.

### <a name="api"></a>API

Le API seguenti verranno modificate o deprecate:

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informazioni riservate rimosse)
    - Usato in precedenza per ottenere singoli tipi di configurazione (inclusi i segreti).
    - A partire dal 3 settembre 2019, questa chiamata API restituirà i singoli tipi di configurazione con segreti omessi. Per ottenere tutte le configurazioni, inclusi i segreti, usare la nuova chiamata POST /configurations. Per ottenere solo le impostazioni del gateway, usare la nuova chiamata POST /getGatewaySettings.
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (deprecato)
    - Usato in precedenza per ottenere tutte le configurazioni (inclusi i segreti)
    - A partire dal 3 settembre 2019, questa chiamata API verrà deprecata e non sarà più supportata. Per ottenere tutte le configurazioni in futuro, usare la nuova chiamata POST /configurations. Per ottenere configurazioni con parametri sensibili omessi, usare la chiamata GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (deprecato)
    - Usato in precedenza per aggiornare le credenziali del gateway.
    - A partire dal 3 settembre 2019, questa chiamata API verrà deprecata e non sarà più supportata. In alternativa, usare il nuovo POST /updateGatewaySettings.

Sono state aggiunte le API sostitutive seguenti:</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Usare questa API per ottenere tutte le configurazioni, inclusi i segreti.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Usare questa API per ottenere le impostazioni del gateway.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Usare questa API per aggiornare le impostazioni del gateway (nome utente e/o password).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools per Visual Studio Code

Se si usa la versione 1.1.1 o successiva, eseguire l'aggiornamento alla versione più recente di [Azure HDInsight Tools per](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) Visual Studio Code per evitare interruzioni.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Se si usa la versione 3.20.0 o successiva, eseguire l'aggiornamento alla versione più recente del [plug-in Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) per evitare interruzioni.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Strumenti di Analisi di flusso e Azure Data Lake per Visual Studio

Eseguire l'aggiornamento alla versione 2.3.9000.1 o successiva di [Azure Data Lake](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) e strumenti di analisi di flusso per Visual Studio evitare interruzioni.  Per informazioni sull'aggiornamento, vedere la documentazione [di Update Data Lake Tools for Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Se si usa la versione 3.15.0 o successiva, eseguire l'aggiornamento alla versione più recente del Azure Toolkit for Eclipse [per](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) evitare interruzioni.

### <a name="sdk-for-net"></a>SDK per .NET

#### <a name="versions-1x-and-2x"></a>Versioni 1.x e 2.x

Eseguire [l'aggiornamento alla versione 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) di HDInsight SDK per .NET. Se si usa un metodo interessato da queste modifiche, possono essere necessarie modifiche minime al codice:

- `ClusterOperationsExtensions.GetClusterConfigurations` non **restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare `ClusterOperationsExtensions.ListConfigurations` in futuro.  Si noti che gli utenti con il ruolo 'Reader' non potranno usare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster.
    - Per recuperare solo le credenziali del gateway HTTP, usare `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` è ora deprecato ed è stato sostituito da `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` è ora deprecato ed è stato sostituito da `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` sono ora deprecati. HTTP è ora sempre abilitato, quindi questi metodi non sono più necessari.

#### <a name="versions-3x-and-up"></a>Versioni 3.x e successive

Eseguire [l'aggiornamento alla versione 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) o successiva di HDInsight SDK per .NET. Se si usa un metodo interessato da queste modifiche, potrebbero essere necessarie modifiche minime al codice:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get) non **restituirà più parametri sensibili come** le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) in futuro.Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di usare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) è ora deprecato ed è stato sostituito da [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) e [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) sono ora deprecati. HTTP è ora sempre abilitato, quindi questi metodi non sono più necessari.

### <a name="sdk-for-python"></a>SDK per Python

Eseguire [l'aggiornamento alla versione 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) o successiva di HDInsight SDK per Python. Se si usa un metodo interessato da queste modifiche, potrebbero essere necessarie modifiche minime al codice:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) non **restituirà più parametri sensibili come** le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) in futuro.Si noti che gli utenti con il ruolo "Lettore" non saranno in grado di usare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) è ora deprecato ed è stato sostituito da [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>SDK per Java

Eseguire [l'aggiornamento alla versione 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) o successiva di HDInsight SDK per Java. Se si usa un metodo interessato da queste modifiche, potrebbero essere necessarie modifiche minime al codice:

- `ConfigurationsInner.get` non **restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
- `ConfigurationsInner.update` è ora deprecato.

### <a name="sdk-for-go"></a>SDK For Go

Eseguire [l'aggiornamento alla versione 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) o successiva di HDInsight SDK per Go. Se si usa un metodo interessato da queste modifiche, possono essere necessarie modifiche minime al codice:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) non **restituirà più parametri sensibili** come le chiavi di archiviazione (core-site) o le credenziali HTTP (gateway).
    - Per recuperare tutte le configurazioni, inclusi i parametri sensibili, usare [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) in futuro.Si noti che gli utenti con il ruolo 'Reader' non potranno usare questo metodo. Ciò consente un controllo granulare sugli utenti che possono accedere a informazioni riservate per un cluster. 
    - Per recuperare solo le credenziali del gateway HTTP, usare [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) è ora deprecato ed è stato sostituito da [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Eseguire [l'aggiornamento ad Az PowerShell versione 2.0.0](https://www.powershellgallery.com/packages/Az) o successiva per evitare interruzioni.  Se si usa un metodo interessato da queste modifiche, potrebbero essere necessarie modifiche minime al codice.
- `Grant-AzHDInsightHttpServicesAccess` è ora deprecato ed è stato sostituito dal nuovo `Set-AzHDInsightGatewayCredential` cmdlet .
- `Get-AzHDInsightJobOutput` è stato aggiornato per supportare l'accesso granulare in base al ruolo alla chiave di archiviazione.
    - Gli utenti con ruolo di collaboratore, proprietario oppure operatore cluster HDInsight non sono interessati.
    - Gli utenti con solo il ruolo Lettore dovranno specificare il `DefaultStorageAccountKey` parametro in modo esplicito.
- `Revoke-AzHDInsightHttpServicesAccess` è ora deprecato. HTTP è ora sempre abilitato, quindi questo cmdlet non è più necessario.
 Vedere [az. Guida alla migrazione di HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) per altri dettagli.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Aggiungere l'assegnazione del ruolo Operatore cluster HDInsight a un utente

Un utente [](../role-based-access-control/built-in-roles.md#owner) con il ruolo Proprietario può assegnare il ruolo Operatore cluster [HDInsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) agli utenti a cui si vuole accedere in lettura/scrittura ai valori di configurazione sensibili del cluster HDInsight, ad esempio le credenziali del gateway del cluster e le chiavi dell'account di archiviazione.

### <a name="using-the-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Il modo più semplice per aggiungere questa assegnazione di ruolo è usare il comando nell'interfaccia della `az role assignment create` riga di comando di Azure.

> [!NOTE]
> Questo comando deve essere eseguito da un utente con il ruolo Proprietario, perché solo l'utente può concedere queste autorizzazioni. è il nome dell'entità servizio o dell'indirizzo di posta elettronica dell'utente a cui si vuole assegnare `--assignee` il ruolo Operatore cluster HDInsight. Se viene visualizzato un errore di autorizzazioni insufficienti, vedere le domande frequenti seguenti.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Concedere il ruolo a livello di risorsa (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Concedere il ruolo a livello di gruppo di risorse

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Concedere il ruolo a livello di sottoscrizione

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

In alternativa, è possibile usare il portale di Azure per aggiungere l'assegnazione del ruolo Operatore cluster HDInsight a un utente. Vedere la documentazione [Assegnare i ruoli di Azure usando portale di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="faq"></a>Domande frequenti

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Perché viene visualizzata una risposta 403 (Accesso negato) dopo l'aggiornamento delle richieste e/o dello strumento API?

Le configurazioni cluster si basano ora sul controllo granulare degli accessi in base al ruolo e richiedono `Microsoft.HDInsight/clusters/configurations/*` l'autorizzazione per accedervi. Per ottenere questa autorizzazione, assegnare il ruolo Operatore cluster HDInsight, Collaboratore o Proprietario all'utente o all'entità servizio che tenta di accedere alle configurazioni.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Perché viene visualizzato il messaggio "Privilegi insufficienti per completare l'operazione" quando si esegue il comando dell'interfaccia della riga di comando di Azure per assegnare il ruolo Operatore cluster HDInsight a un altro utente o entità servizio?

Oltre ad avere il ruolo Proprietario, l'utente o l'entità servizio che esegue il comando deve avere autorizzazioni Azure AD sufficienti per cercare gli ID oggetto dell'assegnatare. Questo messaggio indica che le autorizzazioni Azure AD insufficienti. Provare a sostituire l'argomento con e specificare l'ID oggetto dell'assegnatare come parametro anziché il nome (o l'ID entità nel caso di `-–assignee` `–assignee-object-id` un'identità gestita). Per altre informazioni, vedere la sezione parametri facoltativi della [documentazione az role assignment create.](/cli/azure/role/assignment#az_role_assignment_create)

Se il problema persiste, contattare l'amministratore Azure AD per acquisire le autorizzazioni corrette.

### <a name="what-will-happen-if-i-take-no-action"></a>Cosa accade se non si intraprendere alcuna azione?

A partire dal 3 settembre 2019, le chiamate non restituiscono più informazioni e la chiamata non restituirà più parametri sensibili, ad esempio le chiavi dell'account di archiviazione o la `GET /configurations` `POST /configurations/gateway` password del `GET /configurations/{configurationName}` cluster. Lo stesso vale per i metodi SDK e i cmdlet di PowerShell corrispondenti.

Se si usa una versione precedente di uno degli strumenti per Visual Studio, VSCode, IntelliJ o Eclipse menzionati in precedenza, questi non funzioneranno più fino all'aggiornamento.

Per informazioni più dettagliate, vedere la sezione corrispondente di questo documento per lo scenario.
