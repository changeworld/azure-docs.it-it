---
title: Creare ambienti con più macchine virtuali e risorse PaaS con modelli
description: Informazioni su come creare ambienti con più macchine virtuali e risorse PaaS in Azure DevTest Labs con un modello di Azure Resource Manager
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: f285acffe642a85fa27792ee51ea67a57f6d35a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790114"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Creare ambienti con più macchine virtuali e risorse PaaS con i modelli di Azure Resource Manager

Azure DevTest Labs ambienti consentono agli utenti di distribuire facilmente infrastrutture complesse in modo coerente entro i confini del lab. È possibile usare [Azure Resource Manager per](../azure-resource-manager/templates/template-syntax.md) creare ambienti con set di risorse in DevTest Labs. Questi ambienti possono contenere tutte le risorse di Azure che Resource Manager modelli possono creare.

È possibile [aggiungere facilmente una macchina virtuale (VM) alla](devtest-lab-add-vm.md) volta a un lab usando il portale di Azure . [](https://portal.azure.com) Tuttavia, scenari come app Web multilivello o una farm di SharePoint necessitano di un meccanismo per creare più macchine virtuali in un unico passaggio. Usando i Azure Resource Manager, è possibile definire l'infrastruttura e la configurazione della soluzione di Azure e distribuire ripetutamente più macchine virtuali in uno stato coerente.

Azure Resource Manager modelli offrono anche i vantaggi seguenti:

- Azure Resource Manager i modelli vengono caricati direttamente dal repository github o Azure Repos del controllo del codice sorgente.
- Gli utenti possono creare un ambiente selezionando un modello di Azure Resource Manager configurato dal portale di Azure, come con altri tipi di [basi di macchine virtuali.](devtest-lab-comparing-vm-base-image-types.md)
- È possibile effettuare il provisioning delle risorse PaaS di Azure e delle macchine virtuali IaaS in un ambiente da un Azure Resource Manager modello.
- È possibile tenere traccia del costo degli ambienti nel lab, oltre alle singole macchine virtuali create da altri tipi di basi. Le risorse PaaS vengono create e verranno visualizzate nella verifica dei costi. Tuttavia, l'arresto automatico della macchina virtuale non si applica alle risorse PaaS.

Per altre informazioni sui vantaggi dell'uso di modelli Resource Manager per distribuire, aggiornare o eliminare molte risorse lab in un'unica operazione, vedere Vantaggi dell'uso dei [modelli Resource Manager lab.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Quando si usa un modello Resource Manager come base per creare macchine virtuali del lab, esistono alcune differenze tra la creazione di più macchine virtuali o di una singola macchina virtuale. Per altre informazioni, vedere [Usare il modello Azure Resource Manager macchina virtuale.](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>Usare gli ambienti pubblici di DevTest Labs
Azure DevTest Labs un [repository](https://github.com/Azure/azure-devtestlab/tree/master/Environments) pubblico di modelli Azure Resource Manager che è possibile usare per creare ambienti senza dover connettersi a un'origine GitHub esterna. Questo repository pubblico è simile al repository pubblico di elementi disponibili nel portale di Azure per ogni lab creato. Il repository dell'ambiente consente di iniziare rapidamente a usare modelli di ambiente pre-creati con pochi parametri di input. Questi modelli offrono un'esperienza introduttiva uniforme per le risorse PaaS all'interno dei lab.

Nel repository pubblico, il team DevTest Labs e altri utenti hanno creato e condiviso modelli usati di frequente come App Web di Azure, Service Fabric Cluster e un ambiente farm di SharePoint di sviluppo. È possibile usare questi modelli direttamente o personalizzarli in base alle proprie esigenze. Per altre informazioni, vedere [Configurare e usare ambienti pubblici in DevTest Labs](devtest-lab-configure-use-public-environments.md). Dopo aver creato i propri modelli, è possibile archiviarli in questo repository per condividerli con altri utenti o configurare un repository Git personalizzato.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Creare repository di modelli personalizzati

Come una delle procedure consigliate per l'infrastruttura come codice e la configurazione come codice, è consigliabile gestire i modelli di ambiente nel controllo del codice sorgente. Azure DevTest Labs questa procedura e carica tutti i modelli Azure Resource Manager direttamente da GitHub o Azure Repos repository. Di conseguenza, è possibile usare modelli Resource Manager per l'intero ciclo di rilascio, dall'ambiente di test all'ambiente di produzione.

Esistono diverse regole da seguire per organizzare i modelli Azure Resource Manager in un repository:

- È necessario assegnare al file modello master il *nomeazuredeploy.jsin*.

- Se si desidera utilizzare i valori dei parametri definiti in un file di parametri, il file dei parametri deve essere denominato *azuredeploy.parameters.jsin*.

  È possibile usare i parametri `_artifactsLocation` e `_artifactsLocationSasToken` per costruire il valori URI parametersLink e consentire a DevTest Labs di gestire automaticamente i modelli nidificati. Per altre informazioni, vedere Distribuire modelli [di Azure Resource Manager annidati per ambienti di test.](deploy-nested-template-environments.md)

- È possibile definire i metadati per specificare il nome visualizzato del modello e la *descrizione* in un file denominatometadata.jsin , come indicato di seguito:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![File principali del modello di Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Aggiungere repository di modelli al lab

Dopo aver creato e configurato il repository, è possibile aggiungerlo al lab usando il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco dei lab selezionare il lab desiderato.
1. Nel riquadro Panoramica **del** lab selezionare Configurazione **e criteri.**

   ![Configurazione e criteri](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **Nell'elenco Impostazioni di configurazione** e criteri selezionare **Repository.** Il repository **public artifact repo** viene generato automaticamente per tutti i lab e si connette al repository GitHub pubblico [di DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Per aggiungere il repository Azure Resource Manager modelli, selezionare **Aggiungi.**

   ![Repository pubblico](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Nel **riquadro Repository immettere** le informazioni seguenti:

   - **Nome:** immettere un nome di repository da usare nel lab.
   - **URL clone Git:** immettere l'URL clone HTTPS Git da GitHub o Azure Repos.
   - **Ramo** (facoltativo): immettere il nome del ramo per accedere alle definizioni Azure Resource Manager modello.
   - **Token di accesso personale:** immettere il token di accesso personale usato per accedere in modo sicuro al repository.
     - Per ottenere il token da Azure Repos, nel profilo selezionare Impostazioni utente Token di accesso  >    >  **personali di sicurezza.**
     - Per ottenere il token da GitHub, nel profilo selezionare **Impostazioni**  >  **Impostazioni Developer Token** di accesso  >  **personali.**
   - **Percorsi cartella:** immettere il percorso della cartella relativo all'URI del clone Git per le definizioni degli artefatti o le definizioni Azure Resource Manager modello.

1. Selezionare **Salva**.

   ![Aggiungere un nuovo repository](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Dopo aver aggiunto un Azure Resource Manager al lab, gli utenti del lab possono creare ambienti usando il modello.

## <a name="configure-access-rights-for-lab-users"></a>Configurare i diritti di accesso per gli utenti del lab

Gli utenti del lab **hanno il** ruolo Lettore per impostazione predefinita, quindi non possono modificare le risorse in un gruppo di risorse dell'ambiente. Ad esempio, non possono arrestare o avviare le risorse.

Per assegnare agli utenti del lab il ruolo **Collaboratore** in modo che possano modificare le risorse nei propri ambienti, seguire questa procedura:

1. Nella finestra [portale di Azure](https://portal.azure.com), nel riquadro  Panoramica del lab selezionare Configurazione e criteri **e** quindi selezionare **Impostazioni lab**.

1. Nel riquadro **Impostazioni lab** selezionare **Collaboratore** e quindi Selezionare **Salva** per concedere autorizzazioni di scrittura agli utenti del lab.

   ![Configurare i diritti di accesso per l'utente del lab](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

La sezione successiva illustra la creazione di ambienti da un Azure Resource Manager modello.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Creare ambienti da modelli nel portale di Azure

Dopo aver aggiunto un modello Azure Resource Manager lab, gli utenti del lab possono creare ambienti nel portale di Azure seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nella pagina del lab selezionare **Aggiungi**.

1. Nel **riquadro Scegliere una base** vengono visualizzate le immagini di base che è possibile usare, con i modelli Azure Resource Manager elencati per primi. Selezionare il Azure Resource Manager modello desiderato.

   ![Scegli una base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. Nel riquadro **Aggiungi** immettere un valore **nome ambiente** da visualizzare per gli utenti dell'ambiente.

   Il Azure Resource Manager modello definisce il resto dei campi di input. Se il modello *azuredeploy.parameter.jsfile* definisce valori predefiniti, i campi di input mostrano tali valori.

   Per i parametri di tipo *secure string*, è possibile usare segreti dall'Azure Key Vault. Per informazioni sull'archiviazione dei segreti in un insieme di credenziali delle chiavi e sul loro uso durante la creazione di risorse lab, vedere [Archiviare](devtest-lab-store-secrets-in-key-vault.md)segreti in Azure Key Vault .  

   ![Riquadro Aggiungi](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > I valori dei parametri seguenti non vengono visualizzati nei campi di input, anche se vengono specificati dal modello. Il modulo mostra invece campi di input vuoti in cui gli utenti del lab devono immettere valori durante la creazione dell'ambiente.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Selezionare **Aggiungi** per creare l'ambiente.

   L'ambiente avvia immediatamente il provisioning, con lo stato visualizzato **nell'elenco Macchine virtuali.** Il lab crea automaticamente un nuovo gruppo di risorse per effettuare il provisioning di tutte le risorse definite nel Azure Resource Manager modello.

1. Dopo aver creato l'ambiente, selezionare l'ambiente nell'elenco **Macchine** virtuali per aprire il riquadro Gruppo di risorse ed esplorare tutte le risorse di cui è stato effettuato il provisioning dell'ambiente.

   ![Risorse dell'ambiente](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   È anche possibile espandere l'ambiente per visualizzare solo l'elenco delle macchine virtuali di cui è stato effettuato il provisioning.

   ![Elenco delle macchine virtuali](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selezionare uno degli ambienti per visualizzare le azioni disponibili, ad esempio l'applicazione di elementi, il collegamento di dischi dati, la modifica del tempo di arresto automatico e altro ancora.

   ![Azioni dell'ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatizzare la creazione di ambienti con PowerShell

È possibile usare il portale di Azure per aggiungere un singolo ambiente a un lab, ma quando uno scenario di sviluppo o test deve creare più ambienti, la distribuzione automatizzata è un'esperienza migliore.

Prima di procedere, assicurarsi di avere un modello Azure Resource Manager che definisce le risorse da creare. [Aggiungere e configurare il modello in un repository Git](#configure-your-own-template-repositories)e aggiungere il repository al [lab](#add-template-repositories-to-the-lab).

Lo script di esempio seguente crea un ambiente nel lab. I commenti consentono di comprendere meglio lo script.

1. Salvare lo script di PowerShell di esempio seguente nel disco rigido *comedeployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Eseguire lo script come indicato di seguito, usando i valori specifici per SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (cartella nel repository Git) e EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

È anche possibile usare l'interfaccia della riga di comando di Azure per distribuire le risorse Resource Manager modelli. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Solo un utente con autorizzazioni di proprietario lab può creare macchine virtuali da un modello di Resource Manager usando Azure PowerShell. Se si vuole automatizzare la creazione di macchine virtuali usando un modello Resource Manager e si hanno solo le autorizzazioni utente, è possibile usare il comando dell'interfaccia della riga di comando [az lab vm create.](/cli/azure/lab/vm#az_lab_vm_create)

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Resource Manager del modello in DevTest Labs

Quando si usano modelli di Resource Manager in DevTest Labs, tenere presenti queste limitazioni:

- Non è possibile creare formule o immagini personalizzate dalle macchine virtuali del lab create da un modello Resource Manager lab.

- La maggior parte dei criteri non viene valutata quando si distribuiscono Resource Manager modelli.

Ad esempio, si potrebbe avere un criterio lab che un utente può creare solo cinque macchine virtuali. Tuttavia un utente può distribuire un modello di Resource Manager per la creazione di varie macchine virtuali. I criteri che non vengono valutati includono:

  - Numero di macchine virtuali per utente

  - Numero di macchine virtuali premium per utente del lab

  - Numero di dischi premium per utente del lab

## <a name="next-steps"></a>Passaggi successivi
- Dopo aver creato una macchina virtuale, è possibile connettersi alla macchina virtuale selezionando **Connetti** nel riquadro di gestione della macchina virtuale.
- Visualizzare e gestire le risorse in ambiente selezionando l'ambiente nell'elenco **My virtual machines (Macchine virtuali personali)** nel lab.
- Esplorare i modelli [Azure Resource Manager dalla raccolta di modelli di avvio rapido di Azure.](https://github.com/Azure/azure-quickstart-templates)
