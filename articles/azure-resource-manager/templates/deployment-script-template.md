---
title: Usare gli script di distribuzione nei modelli | Microsoft Docs
description: usare gli script di distribuzione nei modelli di Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: d35deb978b3b60b73ac393b241471cb528817d35
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536965"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Usare gli script di distribuzione nei modelli arm

Informazioni su come usare gli script di distribuzione nei modelli di Risorse di Azure (modelli arm). Con un nuovo tipo di risorsa denominato `Microsoft.Resources/deploymentScripts` , gli utenti possono eseguire script nelle distribuzioni di modelli ed esaminare i risultati dell'esecuzione. Questi script possono essere usati per eseguire passaggi personalizzati, ad esempio:

- Aggiungere utenti a una directory
- Eseguire operazioni sul piano dati, ad esempio, copiare i BLOB o il database di inizializzazione
- Cercare e convalidare un codice di licenza
- Creare un certificato autofirmato
- Creare un oggetto in Azure AD
- Cercare blocchi di indirizzi IP dal sistema personalizzato

I vantaggi dello script di distribuzione:

- Semplifica la scrittura di codice, l'uso e l'esecuzione del debug. È possibile sviluppare script di distribuzione negli ambienti di sviluppo preferiti. Gli script possono essere incorporati in modelli o in file di script esterni.
- È possibile specificare la piattaforma e il linguaggio dello script. Attualmente gli script di distribuzione di Azure PowerShell e dell'interfaccia della riga di comando di Azure nell'ambiente Linux sono supportati.
- Consente di specificare gli argomenti della riga di comando da passare allo script.
- Può specificare gli output dello script e passarli nuovamente alla distribuzione.

La risorsa script di distribuzione è disponibile solo nelle aree in cui è disponibile l'istanza di contenitore di Azure.  Vedere [Disponibilità di risorse per Istanze di Azure Container nelle aree di Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili le opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Le due risorse create automaticamente vengono in genere eliminate dal servizio script quando l'esecuzione dello script di distribuzione raggiunge uno stato finale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> L'API della risorsa deploymentScripts versione 2020-10-01 supporta [OnBehalfofTokens (OBO).](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md) Usando OBO, il servizio script di distribuzione usa il token dell'entità di distribuzione per creare le risorse sottostanti per l'esecuzione degli script di distribuzione, che includono l'istanza di Azure Container, l'account di archiviazione di Azure e le assegnazioni di ruolo per l'identità gestita. Nella versione precedente dell'API, l'identità gestita viene usata per creare queste risorse.
> La logica di ripetizione dei tentativi per l'accesso ad Azure è ora incorporata nello script wrapper. Se si concedono autorizzazioni nello stesso modello in cui si eseguono gli script di distribuzione. Il servizio script di distribuzione ritenta l'accesso per 10 minuti con un intervallo di 10 secondi fino a quando non viene replicata l'assegnazione del ruolo identità gestita.

## <a name="configure-the-minimum-permissions"></a>Configurare le autorizzazioni minime

Per l'API dello script di distribuzione versione 2020-10-01 o successiva, l'entità di distribuzione viene usata per creare le risorse sottostanti necessarie per l'esecuzione della risorsa script di distribuzione, ovvero un account di archiviazione e un'istanza di contenitore di Azure. Se lo script deve eseguire l'autenticazione in Azure ed eseguire azioni specifiche di Azure, è consigliabile fornire allo script un'identità gestita assegnata dall'utente. L'identità gestita deve avere l'accesso necessario per completare l'operazione nello script.

Per configurare le autorizzazioni con privilegi minimi, è necessario:

- Assegnare un ruolo personalizzato con le proprietà seguenti all'entità di distribuzione:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Se il Archiviazione di Azure e i provider di risorse dell'istanza di Contenitore di Azure non sono stati registrati, è necessario aggiungere `Microsoft.Storage/register/action` anche e `Microsoft.ContainerInstance/register/action` .

- Se viene usata un'identità gestita, l'entità di distribuzione deve avere il ruolo **Operatore** identità gestita (un ruolo predefinito) assegnato alla risorsa identità gestita.

## <a name="sample-templates"></a>Modelli di esempio

Il codice JSON seguente è un esempio. Per altre informazioni, vedere lo schema del [modello più recente.](/azure/templates/microsoft.resources/deploymentscripts)

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> L'esempio è a scopo dimostrativo. Le proprietà `scriptContent` e `primaryScriptUri` non possono coesistere in un modello.

> [!NOTE]
> ScriptContent _mostra_ uno script con più righe.  Le portale di Azure e Azure DevOps pipeline non possono analizzare uno script di distribuzione con più righe. È possibile concatenare i comandi di PowerShell (usando punti e virgola o _\\ r \\ n_ _\\ o n_) in un'unica riga oppure usare la proprietà con un file di script `primaryScriptUri` esterno. Sono disponibili molti strumenti di escape/unescape di stringhe JSON gratuiti. ad esempio [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Dettagli sui valori delle proprietà:

- `identity`: per l'API script di distribuzione versione 2020-10-01 o successiva, un'identità gestita assegnata dall'utente è facoltativa, a meno che non sia necessario eseguire azioni specifiche di Azure nello script.  Per l'API versione 2019-10-01-preview, è necessaria un'identità gestita perché il servizio script di distribuzione la usa per eseguire gli script. Quando viene specificata la proprietà Identity, il servizio script chiama `Connect-AzAccount -Identity` prima di richiamare lo script utente. Attualmente è supportata solo l'Identità gestita assegnata dall'utente di Azure. Per accedere con un'identità diversa, è possibile chiamare [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) nello script.
- `kind`: specificare il tipo di script. Attualmente sono supportati Azure PowerShell e gli script dell'interfaccia della riga di comando di Azure. I valori sono **AzurePowerShell** e **AzureCLI**.
- `forceUpdateTag`: se si modifica questo valore tra distribuzioni di modelli, lo script di distribuzione viene rieseguiti. Se si usano le funzioni o , entrambe le funzioni possono essere usate solo `newGuid()` nel valore predefinito per un `utcNow()` parametro. Per altre informazioni, vedere [Eseguire lo script più di una volta](#run-script-more-than-once).
- `containerSettings`: specificare le impostazioni per personalizzare Istanza di Azure Container. Lo script di distribuzione richiede una nuova istanza di Azure Container. Non è possibile specificare un'istanza di Azure Container esistente. Tuttavia, è possibile personalizzare il nome del gruppo di contenitori usando `containerGroupName` . Se non viene specificato, il nome del gruppo viene generato automaticamente.
- `storageAccountSettings`: specificare le impostazioni per l'uso di un account di archiviazione esistente. Se `storageAccountName` non viene specificato, viene creato automaticamente un account di archiviazione. Vedere [Usare un account di archiviazione esistente](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: specificare la versione del modulo da usare. Vedere un elenco delle [versioni Azure PowerShell supportate](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Vedere un elenco delle versioni supportate [dell'interfaccia della riga di comando di Azure.](https://mcr.microsoft.com/v2/azure-cli/tags/list)

  >[!IMPORTANT]
  > Lo script di distribuzione usa le immagini disponibili dell'interfaccia della riga di comando Microsoft Container Registry (MCR). La certificazione di un'immagine dell'interfaccia della riga di comando per uno script di distribuzione richiede circa un mese. Non usare le versioni dell'interfaccia della riga di comando rilasciate negli ultimi 30 giorni. Per trovare le date di rilascio delle immagini, vedere [Note sulla versione dell'interfaccia della riga di comando di Azure](/cli/azure/release-notes-azure-cli). Se viene usata una versione non supportata, il messaggio di errore elenca le versioni supportate.

- `arguments`: Specificare i valori del parametro. I valori sono separati da uno spazio.

  Script di distribuzione suddivide gli argomenti in una matrice di stringhe richiamando la chiamata di sistema [CommandLineToArgvW.](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) Questo passaggio è necessario perché gli argomenti vengono passati come proprietà [di](/rest/api/container-instances/containergroups/createorupdate#containerexec) comando all'istanza di Azure Container e la proprietà del comando è una matrice di stringhe.

  Se gli argomenti contengono caratteri di escape, usare [JsonEscaper per](https://www.jsonescaper.com/) eseguire il doppio escape dei caratteri. Incollare la stringa di escape originale nello strumento e quindi selezionare **Escape**.  Lo strumento restituisce una stringa con doppio carattere di escape. Nel modello di esempio precedente, ad esempio, l'argomento è `-name \"John Dole\"` . La stringa con carattere di escape è `-name \\\"John Dole\\\"` .

  Per passare un parametro di modello ARM di tipo object come argomento, convertire l'oggetto in una stringa usando la [funzione string()](./template-functions-string.md#string) e quindi usare la funzione [replace()](./template-functions-string.md#replace) per sostituire qualsiasi in `\"` `\\\"` . Ad esempio:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Per altre informazioni, vedere il [modello di esempio](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: specificare le variabili di ambiente da passare allo script. Per altre informazioni, vedere [Sviluppare script di distribuzione](#develop-deployment-scripts).
- `scriptContent`: specificare il contenuto dello script. Per eseguire uno script esterno, usare `primaryScriptUri`. Per esempi, vedere [Usare script inline](#use-inline-scripts) e [Usare script esterni](#use-external-scripts).
- `primaryScriptUri`: specificare un URL accessibile pubblicamente allo script di distribuzione primario con le estensioni di file supportate. Per altre informazioni, vedere [Usare script esterni.](#use-external-scripts)
- `supportingScriptUris`: specificare una matrice di URL accessibili pubblicamente per i file di supporto chiamati in `scriptContent` o `primaryScriptUri` . Per altre informazioni, vedere [Usare script esterni.](#use-external-scripts)
- `timeout`: specificare il tempo di esecuzione dello script massimo consentito nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**.
- `cleanupPreference`. Specificare la preferenza per la pulizia delle risorse di distribuzione quando l'esecuzione dello script si trova in uno stato terminale. L'impostazione predefinita è **Sempre**, che indica l'eliminazione delle risorse nonostante lo stato del terminale (Riuscito, Non riuscito, Annullato). Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).
- `retentionInterval`: specificare l'intervallo per il quale il servizio mantiene le risorse dello script di distribuzione dopo che l'esecuzione dello script di distribuzione raggiunge uno stato terminale. Le risorse dello script di distribuzione verranno eliminate alla scadenza di tale durata. La durata è basata sul [modello ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). L'intervallo di conservazione è compreso tra 1 e 26 ore (PT26H). Questa proprietà viene usata quando l'opzione `cleanupPreference` è impostata su **OnExpiration**. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Altri esempi

- [Esempio 1:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)creare un insieme di credenziali delle chiavi e usare lo script di distribuzione per assegnare un certificato all'insieme di credenziali delle chiavi.
- [Esempio 2:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)creare un gruppo di risorse a livello di sottoscrizione, creare un insieme di credenziali delle chiavi nel gruppo di risorse e quindi usare lo script di distribuzione per assegnare un certificato all'insieme di credenziali delle chiavi.
- [Esempio 3:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)creare un'identità gestita assegnata dall'utente, assegnare il ruolo collaboratore all'identità a livello di gruppo di risorse, creare un insieme di credenziali delle chiavi e quindi usare lo script di distribuzione per assegnare un certificato all'insieme di credenziali delle chiavi.

## <a name="use-inline-scripts"></a>Usare script inline

Il modello seguente include una risorsa definita con il tipo `Microsoft.Resources/deploymentScripts`. La parte evidenziata è lo script inline.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Poiché gli script di distribuzione inline sono racchiusi tra virgolette doppie, le stringhe all'interno degli script di distribuzione devono essere precedute da una barra rovesciata (**&#92;**) o racchiuse tra virgolette singole. È anche possibile prendere in considerazione l'uso della sostituzione di stringhe come illustrato nell'esempio JSON precedente.

Lo script richiede un parametro e genera l'output del valore del parametro. `DeploymentScriptOutputs` viene usato per l'archiviazione degli output. Nella sezione outputs la riga `value` mostra come accedere ai valori archiviati. `Write-Output` è usato a scopo di debug. Per informazioni su come accedere al file di output, vedere Monitorare e risolvere i problemi [relativi agli script di distribuzione.](#monitor-and-troubleshoot-deployment-scripts) Per le descrizioni delle proprietà, vedere [Modelli di esempio](#sample-templates).

Per eseguire lo script, selezionare **Prova** per aprire Cloud Shell e quindi incollare il codice seguente nel riquadro della shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

L'output è simile al seguente:

![Output dello script di distribuzione del modello di Resource Manager hello world](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Usare script esterni

Oltre agli script inline, è anche possibile usare file di script esterni. Sono supportati solo gli script di PowerShell primari con l'estensione di file _ps1_. Per gli script dell'interfaccia della riga di comando, gli script primari possono avere estensioni (o senza estensione), purché gli script siano script Bash validi. Per usare file di script esterni, sostituire `scriptContent` con `primaryScriptUri`. Ad esempio:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Per altre informazioni, vedere il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

I file di script esterni devono essere accessibili. Per proteggere i file di script archiviati negli account di archiviazione di Azure, generare un token di firma di accesso condiviso e includerlo nell'URI per il modello. Impostare l'ora di scadenza in modo da garantire un tempo sufficiente per completare la distribuzione. Per altre informazioni, vedere Distribuire [un modello arm privato con token di firma di accesso condiviso.](./secure-template-with-sas-token.md)

L'utente è responsabile di garantire l'integrità degli script a cui fa riferimento lo script di distribuzione, `primaryScriptUri` o `supportingScriptUris` . Fare riferimento solo a script attendibili.

## <a name="use-supporting-scripts"></a>Usare script di supporto

È possibile separare le logiche complesse in uno o più file di script di supporto. Se necessario, la proprietà `supportingScriptUris` consente di fornire una matrice di URI ai file di script di supporto:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

I file script di supporto possono essere chiamati sia da script inline che da file di script primari. I file di script di supporto non prevedono alcuna restrizione sull'estensione di file.

I file di supporto vengono copiati in `azscripts/azscriptinput` in fase di esecuzione. Usare il percorso relativo per fare riferimento ai file di supporto da script inline e file di script primari.

## <a name="work-with-outputs-from-powershell-script"></a>Usare gli output dello script di PowerShell

Il modello seguente illustra come passare valori tra due `deploymentScripts` risorse:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

Nella prima risorsa si definisce una variabile denominata `$DeploymentScriptOutputs` e la si usa per archiviare i valori di output. Per accedere al valore di output da un'altra risorsa all'interno del modello, usare:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Usare gli output dello script dell'interfaccia della riga di comando

Diversamente dallo script di distribuzione di PowerShell, il supporto dell'interfaccia della riga di comando/Bash non espone una variabile comune per archiviare gli output degli script, ma è presente una variabile di ambiente denominata che archivia il percorso in cui si trova il file di output dello `AZ_SCRIPTS_OUTPUT_PATH` script. Se uno script di distribuzione viene eseguito da un modello di Resource Manager, questa variabile di ambiente viene impostata automaticamente dalla shell Bash. Il valore di `AZ_SCRIPTS_OUTPUT_PATH` è */mnt/azscripts/azscriptoutput/scriptoutputs.jsin*.

Gli output degli script di distribuzione devono essere salvati nel percorso e gli `AZ_SCRIPTS_OUTPUT_PATH` output devono essere un oggetto stringa JSON valido. Il contenuto del file deve essere salvato come coppia chiave-valore. Ad esempio, una matrice di stringhe viene archiviata come `{ "MyResult": [ "foo", "bar"] }` .  L'archiviazione solo dei risultati della matrice, ad esempio `[ "foo", "bar" ]` , non è valida.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

Nell'esempio precedente viene usato [jq](https://stedolan.github.io/jq/). Viene fornito con le immagini del contenitore. Vedere [Configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Usare un account di archiviazione esistente

Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili le opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Requisiti per l'uso di un account di archiviazione esistente:

- I tipi di account di archiviazione supportati sono:

    | SKU             | Tipo supportato     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Archiviazione, ArchiviazioneV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Archiviazione, ArchiviazioneV2 |
    | Standard_RAGRS  | Archiviazione, ArchiviazioneV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Queste combinazioni supportano condivisioni file. Per altre informazioni, vedere [Creare una condivisione file di Azure](../../storage/files/storage-how-to-create-file-share.md) e Tipi di account di [archiviazione.](../../storage/common/storage-account-overview.md)

- Le regole del firewall dell'account di archiviazione non sono ancora supportate. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../../storage/common/storage-network-security.md).
- L'entità distribuzione deve avere le autorizzazioni per gestire l'account di archiviazione, che include le condivisioni file di lettura, creazione ed eliminazione.

Per specificare un account di archiviazione esistente, aggiungere il codice JSON seguente all'elemento property di `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: specificare il nome dell'account di archiviazione.
- `storageAccountKey`: specificare una delle chiavi dell'account di archiviazione. È possibile usare la [funzione listKeys()](./template-functions-resource.md#listkeys) per recuperare la chiave. Ad esempio:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Vedere [Modelli di esempio](#sample-templates) per un esempio di definizione di `Microsoft.Resources/deploymentScripts` completo.

Quando viene usato un account di archiviazione esistente, il servizio script crea una condivisione file con un nome univoco. Vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources) per informazioni su come il servizio script pulisce la condivisione file.

## <a name="develop-deployment-scripts"></a>Sviluppare script di distribuzione

### <a name="handle-non-terminating-errors"></a>Gestire errori non fatali

È possibile controllare il modo in cui PowerShell risponde agli errori non di terminazione usando la `$ErrorActionPreference` variabile nello script di distribuzione. Se la variabile non è impostata nello script di distribuzione, il servizio script usa il valore predefinito **Continua**.

Il servizio script imposta lo stato di provisioning delle risorse su **Non** riuscito quando lo script rileva un errore nonostante l'impostazione di `$ErrorActionPreference` .

### <a name="use-environment-variables"></a>Usare variabili di ambiente

Lo script di distribuzione usa queste variabili di ambiente:

|Variabile di ambiente|Valore predefinito|Sistema riservato|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|S|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|S|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|S|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Interfaccia della riga di comando di Azure: userscript.sh|S|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|S|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|S|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.jssu|S|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.jssu|S|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

Per altre informazioni sull'uso di `AZ_SCRIPTS_OUTPUT_PATH` , vedere Usare gli output dello script [dell'interfaccia della riga di comando.](#work-with-outputs-from-cli-script)

### <a name="pass-secured-strings-to-deployment-script"></a>Passare stringhe protette allo script di distribuzione

L'impostazione delle variabili di ambiente (EnvironmentVariable) nelle istanze di contenitore consente di offrire la configurazione dinamica dell'applicazione o dello script eseguiti dal contenitore. Lo script di distribuzione gestisce variabili di ambiente non protette e protette in modo analogo all'istanza di contenitore di Azure. Per altre informazioni, vedere [Impostare variabili di ambiente in istanze di contenitore](../../container-instances/container-instances-environment-variables.md#secure-values). Per un esempio, vedere [Modelli di esempio.](#sample-templates)

Le dimensioni massime consentite per le variabili di ambiente sono 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Monitorare e risolvere i problemi relativi agli script di distribuzione

Il servizio script crea un [account di archiviazione](../../storage/common/storage-account-overview.md) (a meno che non si specifichi un account di archiviazione esistente) e un'[istanza di contenitore](../../container-instances/container-instances-overview.md) per l'esecuzione dello script. Se queste risorse vengono create automaticamente dal servizio script, entrambe le risorse hanno il `azscripts` suffisso nei nomi delle risorse.

![Nomi delle risorse dello script di distribuzione del modello di Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Lo script utente, i risultati dell'esecuzione e il file stdout vengono archiviati nelle condivisioni file dell'account di archiviazione. È presente una cartella denominata `azscripts` . Nella cartella sono presenti altre due cartelle per l'input e i file di output: `azscriptinput` e `azscriptoutput` .

La cartella di output contiene un file _executionresult.json_ e il file di output dello script. È possibile visualizzare il messaggio di errore dell'esecuzione dello script in _executionresult.json_. Il file di output viene creato solo quando lo script viene eseguito correttamente. La cartella di input contiene un file di script di sistema di PowerShell e i file di script di distribuzione dell'utente. È possibile sostituire il file di script di distribuzione dell'utente con uno modificato ed eseguire nuovamente lo script di distribuzione dall'istanza di contenitore di Azure.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Dopo aver distribuito una risorsa script di distribuzione, la risorsa viene elencata nel gruppo di risorse nella portale di Azure. Lo screenshot seguente mostra la **pagina Panoramica** di una risorsa script di distribuzione:

![Resource Manager panoramica del portale degli script di distribuzione dei modelli](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Nella pagina di panoramica vengono visualizzate alcune informazioni importanti della risorsa, ad esempio **stato del provisioning,** account di **archiviazione,** istanza **del** contenitore e **log.**

Dal menu a sinistra è possibile visualizzare il contenuto dello script di distribuzione, gli argomenti passati allo script e l'output. È anche possibile esportare un modello per lo script di distribuzione, incluso lo script di distribuzione.

### <a name="use-powershell"></a>Usare PowerShell

Usando Azure PowerShell, è possibile gestire gli script di distribuzione nell'ambito della sottoscrizione o del gruppo di risorse:

- [Get-AzDeploymentScript:](/powershell/module/az.resources/get-azdeploymentscript)ottiene o elenca gli script di distribuzione.
- [Get-AzDeploymentScriptLog:](/powershell/module/az.resources/get-azdeploymentscriptlog)ottiene il log dell'esecuzione di uno script di distribuzione.
- [Remove-AzDeploymentScript:](/powershell/module/az.resources/remove-azdeploymentscript)rimuove uno script di distribuzione e le risorse associate.
- [Save-AzDeploymentScriptLog:](/powershell/module/az.resources/save-azdeploymentscriptlog)salva il log dell'esecuzione di uno script di distribuzione su disco.

`Get-AzDeploymentScript`L'output è simile al seguente:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Usando l'interfaccia della riga di comando di Azure, è possibile gestire gli script di distribuzione nell'ambito della sottoscrizione o del gruppo di risorse:

- [az deployment-scripts delete:](/cli/azure/deployment-scripts#az-deployment-scripts-delete)elimina uno script di distribuzione.
- [az deployment-scripts list:](/cli/azure/deployment-scripts#az-deployment-scripts-list)elenca tutti gli script di distribuzione.
- [az deployment-scripts show:](/cli/azure/deployment-scripts#az-deployment-scripts-show)recupera uno script di distribuzione.
- [az deployment-scripts show-log:](/cli/azure/deployment-scripts#az-deployment-scripts-show-log)mostra i log degli script di distribuzione.

L'output del comando list è simile al seguente:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Usare l'API REST

È possibile ottenere le informazioni sulla distribuzione delle risorse dello script di distribuzione a livello di gruppo di risorse e a livello di sottoscrizione usando l'API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

Nell'esempio seguente viene usato [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

L'output è simile a:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

L'API REST seguente restituisce il log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Funziona solo prima che vengano eliminate le risorse dello script di distribuzione.

Per visualizzare la risorsa deploymentScripts nel portale, selezionare **Mostra tipi nascosti**:

![Script di distribuzione del modello di Resource Manager, mostra tipi nascosti, portale](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Pulire le risorse dello script di distribuzione

Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Le due risorse create automaticamente vengono eliminate dal servizio script quando l'esecuzione dello script di distribuzione raggiunge uno stato finale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per informazioni sui prezzi, vedere [Prezzi di Istanze di Container](https://azure.microsoft.com/pricing/details/container-instances/) e [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Il ciclo di vita di queste risorse è controllato dalle proprietà seguenti nel modello:

- `cleanupPreference`: pulizia delle preferenze quando l'esecuzione dello script entra in uno stato terminale. I valori supportati sono:

  - **Sempre**: Eliminare le risorse create automaticamente una volta che l'esecuzione dello script si trova in uno stato terminale. Se viene usato un account di archiviazione esistente, il servizio script elimina la condivisione file creata nell'account di archiviazione. Poiché la risorsa può essere ancora presente dopo la pulizia delle risorse, il servizio script rende persistenti i risultati dell'esecuzione dello script, ad esempio stdout, output e valore restituito prima dell'eliminazione delle `deploymentScripts` risorse.
  - **OnSuccess**: Eliminare le risorse create automaticamente solo quando l'esecuzione dello script ha esito positivo. Se viene usato un account di archiviazione esistente, il servizio script rimuove la condivisione file solo quando l'esecuzione dello script ha esito positivo. È comunque possibile accedere alle risorse per trovare le informazioni di debug.
  - **OnExpiration:** elimina le risorse create automaticamente solo quando `retentionInterval` l'impostazione è scaduta. Se viene usato un account di archiviazione esistente, il servizio script rimuove la condivisione file, ma mantiene l'account di archiviazione.

- `retentionInterval`: specificare l'intervallo di tempo in cui una risorsa script verrà mantenuta e dopo la quale verrà scaduta ed eliminata.

> [!NOTE]
> Non è consigliabile usare l'account di archiviazione e l'istanza di contenitore generati dal servizio script per altri scopi. Le due risorse potrebbero essere rimosse a seconda del ciclo di vita dello script.

L'istanza del contenitore e l'account di archiviazione vengono eliminati in base a `cleanupPreference` . Tuttavia, se lo script ha esito negativo e non è impostato su Always, il processo di distribuzione mantiene automaticamente il `cleanupPreference` contenitore in esecuzione per un'ora.  È possibile usare questa ora per risolvere i problemi dello script. Se si vuole mantenere il contenitore in esecuzione al termine delle distribuzioni, aggiungere un passaggio di sospensione allo script. Ad esempio, aggiungere [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) alla fine dello script. Se non si aggiunge il passaggio di sospensione, il contenitore viene impostato su uno stato terminale e non è possibile accedervi anche se non è stato ancora eliminato.

## <a name="run-script-more-than-once"></a>Eseguire lo script più di una volta

L'esecuzione dello script di distribuzione è un'operazione idempotente. Se nessuna delle proprietà della risorsa (incluso lo script inline) viene modificata, lo script non viene eseguito quando `deploymentScripts` si ridistribuisce il modello. Il servizio script di distribuzione confronta i nomi delle risorse nel modello con le risorse esistenti nello stesso gruppo di risorse. Se si vuole eseguire lo stesso script di distribuzione più volte, sono disponibili due opzioni:

- Modificare il nome della `deploymentScripts` risorsa. Usare, ad esempio, la funzione di modello [utcNow](./template-functions-date.md#utcnow) come nome della risorsa o come parte del nome della risorsa. La modifica del nome della risorsa crea una nuova `deploymentScripts` risorsa. È buona per mantenere una cronologia dell'esecuzione dello script.

    > [!NOTE]
    > La `utcNow` funzione può essere usata solo nel valore predefinito per un parametro.

- Specificare un valore diverso nella proprietà del modello `forceUpdateTag`. Ad esempio, usare `utcNow` come valore .

> [!NOTE]
> Scrivere script di distribuzione idempotenti. In questo modo si garantisce che, in caso di esecuzione accidentale, non provocheranno modifiche al sistema. Ad esempio, se si usa lo script di distribuzione per creare una risorsa di Azure, verificare che la risorsa non esista prima di crearla, in modo che lo script abbia esito positivo o la risorsa non venga creata nuovamente.

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

È possibile usare un'immagine del contenitore preconfigurato come ambiente di sviluppo dello script di distribuzione. Per altre informazioni, vedere Configurare [l'ambiente di sviluppo per gli script di distribuzione nei modelli.](./deployment-script-template-configure-dev.md)

Dopo aver testato correttamente lo script, è possibile usarlo come script di distribuzione nei modelli.

## <a name="deployment-script-error-codes"></a>Codici di errore dello script di distribuzione

| Codice di errore | Descrizione |
|------------|-------------|
| DeploymentScriptInvalidOperation | La definizione della risorsa dello script di distribuzione nel modello contiene nomi di proprietà non validi. |
| DeploymentScriptResourceConflict | Non è possibile eliminare una risorsa script di distribuzione in stato non terminale e l'esecuzione non ha superato 1 ora. Oppure non può eseguire di nuovo lo stesso script di distribuzione con lo stesso identificatore di risorsa (stessa sottoscrizione, nome del gruppo di risorse e nome della risorsa) ma contemporaneamente contenuto del corpo dello script diverso. |
| DeploymentScriptOperationFailed | L'operazione dello script di distribuzione non è riuscita internamente. Contattare il supporto tecnico Microsoft. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | La chiave di accesso non è stata specificata per l'account di archiviazione esistente.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Un gruppo di contenitori creato dal servizio script di distribuzione è stato modificato esternamente e sono stati aggiunti contenitori non validi. |
| DeploymentScriptContainerGroupInNonterminalState | Due o più risorse script di distribuzione usano lo stesso nome dell'istanza di Azure Container nello stesso gruppo di risorse e una di esse non ha ancora completato l'esecuzione. |
| DeploymentScriptStorageAccountInvalidKind | L'account di archiviazione esistente di tipo BlobBlobStorage o BlobStorage non supporta condivisioni file e non può essere usato. |
| DeploymentScriptStorageAccountInvalidKindAndSku | L'account di archiviazione esistente non supporta condivisioni file. Per un elenco dei tipi di account di archiviazione supportati, vedere [Usare un account di archiviazione esistente.](#use-existing-storage-account) |
| DeploymentScriptStorageAccountNotFound | L'account di archiviazione non esiste o è stato eliminato da un processo o uno strumento esterno. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | L'account di archiviazione specificato ha un endpoint di servizio. Un account di archiviazione con un endpoint di servizio non è supportato. |
| DeploymentScriptStorageAccountInvalidAccessKey | Chiave di accesso non valida specificata per l'account di archiviazione esistente. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Formato della chiave dell'account di archiviazione non valido. Vedere [Gestire le chiavi di accesso dell'account di archiviazione.](../../storage/common/storage-account-keys-manage.md) |
| DeploymentScriptExceededMaxAllowedTime | Il tempo di esecuzione dello script di distribuzione ha superato il valore di timeout specificato nella definizione della risorsa script di distribuzione. |
| DeploymentScriptInvalidOutputs | L'output dello script di distribuzione non è un oggetto JSON valido. |
| DeploymentScriptContainerInstancesServiceLoginFailure | L'identità gestita assegnata dall'utente non è stata in grado di accedere dopo 10 tentativi con un intervallo di 1 minuto. |
| DeploymentScriptContainerGroupNotFound | Un gruppo di contenitori creato dal servizio script di distribuzione è stato eliminato da uno strumento o un processo esterno. |
| DeploymentScriptDownloadFailure | Impossibile scaricare uno script di supporto. Vedere [Usare lo script di supporto](#use-supporting-scripts).|
| DeploymentScriptError | Lo script utente ha generato un errore. |
| DeploymentScriptBootstrapScriptExecutionFailed | Lo script bootstrap ha generato un errore. Lo script bootstrap è lo script di sistema che orchestra l'esecuzione dello script di distribuzione. |
| DeploymentScriptExecutionFailed | Errore sconosciuto durante l'esecuzione dello script di distribuzione. |
| DeploymentScriptContainerInstancesServiceUnavailable | Quando si crea l'istanza del contenitore di Azure, L'istanza di Azure Container ha generato un errore di servizio non disponibile. |
| DeploymentScriptContainerGroupInNonterminalState | Quando si crea l'istanza del contenitore di Azure, un altro script di distribuzione usa lo stesso nome ACI nello stesso ambito (stessa sottoscrizione, nome del gruppo di risorse e nome della risorsa). |
| DeploymentScriptContainerGroupNameInvalid | Il nome dell'istanza del contenitore di Azure specificato non soddisfa i requisiti ACI. Vedere [Risolvere i problemi comuni in Istanze di Azure Container](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare gli script di distribuzione. Per esaminare un'esercitazione relativa a uno script di distribuzione:

> [!div class="nextstepaction"]
> [Esercitazione: Usare gli script di distribuzione nei modelli di Azure Resource Manager](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Modulo Learn: Estendere i modelli arm usando gli script di distribuzione](/learn/modules/extend-resource-manager-template-deployment-scripts/)
