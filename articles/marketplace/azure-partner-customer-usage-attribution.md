---
title: Attribuzione dell'utilizzo dei clienti di Azure
description: Ottenere una panoramica del monitoraggio dell'utilizzo dei clienti per le applicazioni Azure nel Marketplace commerciale e in altri indirizzi IP distribuibile sviluppati dai partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/09/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 8f84d77b5a424d5f7273c7e748c35c52882819c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608394"
---
# <a name="azure-customer-usage-attribution"></a>Attribuzione dell'utilizzo dei clienti di Azure

L'attribuzione dell'utilizzo del cliente associa l'utilizzo dalle risorse di Azure nelle sottoscrizioni dei clienti create durante la distribuzione dell'IP con l'utente come partner. La creazione di queste associazioni nei sistemi Microsoft interni offre maggiore visibilità sul footprint di Azure che esegue il proprio software. Per [applicazione Azure offerte nel Marketplace commerciale](#commercial-marketplace-azure-apps), questa funzionalità di rilevamento ti aiuta a allinearti ai team di vendita Microsoft e a ottenere credito per i programmi partner Microsoft.

L'attribuzione dell'utilizzo da parte dei clienti supporta tre opzioni di distribuzione:

1. Modelli di Azure Resource Manager (le basi comuni delle app di Azure, dette anche nel Marketplace commerciale come "modelli di soluzione" o "app gestite"): i partner creano modelli Gestione risorse per definire l'infrastruttura e la configurazione delle soluzioni Azure. Un modello di Gestione risorse consente ai clienti di distribuire le risorse della soluzione in uno stato coerente e ripetibile.
1. API Azure Resource Manager: i partner possono chiamare le API Gestione risorse per distribuire un modello di Gestione risorse o effettuare direttamente il provisioning dei servizi di Azure.
1. Bonifica: i partner possono usare la bonifica per distribuire un modello di Gestione risorse o distribuire direttamente i servizi di Azure.

Esistono casi d'uso secondari per l'attribuzione dell'utilizzo dei clienti all'esterno del Marketplace commerciale descritto [più avanti in questo articolo](#other-use-cases).

>[!IMPORTANT]
>- L'attribuzione dell'utilizzo dei clienti non è destinata a tenere traccia del lavoro degli integratori di sistemi, dei provider di servizi gestiti o degli strumenti progettati principalmente per la distribuzione e la gestione delle risorse di Azure.
>- L'attribuzione dell'utilizzo dei clienti riguarda le nuove distribuzioni e non supporta il rilevamento delle risorse già distribuite.
>- Non tutti i servizi di Azure sono compatibili con l'attribuzione dell'utilizzo del cliente. I servizi di Azure Kubernetes (AKS) e i set di scalabilità di VM presentano problemi noti che provocano la creazione di report in base all'utilizzo.

## <a name="commercial-marketplace-azure-apps"></a>Marketplace commerciale app di Azure

Il monitoraggio dell'utilizzo di Azure da parte delle app di Azure pubblicate sul Marketplace commerciale è in gran parte automatico. Quando si carica un modello di Gestione risorse come parte della [configurazione tecnica del piano dell'app Azure del Marketplace](https://docs.microsoft.com/azure/marketplace/create-new-azure-apps-offer-solution#define-the-technical-configuration), il centro per i partner aggiunge un ID di rilevamento leggibile da Azure Resource Manager.

Se si usano Azure Resource Manager API, sarà necessario aggiungere l'ID di traccia in base alle [istruzioni](#use-resource-manager-apis) riportate di seguito per passarlo alla Azure Resource Manager mentre il codice distribuisce le risorse. Questo ID è visibile nel centro per i partner nella pagina di configurazione tecnica del piano. 

> [!NOTE]
> Per le app di Azure esistenti, è stata eseguita una migrazione monouso nel 2021 marzo per aggiornare gli ID di rilevamento nella configurazione tecnica di ogni piano. L'utilizzo delle distribuzioni precedenti di tali offerte rimarrà registrato nei sistemi Microsoft.

## <a name="other-use-cases"></a>Altri casi d'uso 

È possibile usare l'attribuzione dell'utilizzo del cliente per tenere traccia dell'utilizzo di soluzioni di Azure non disponibili nel Marketplace commerciale. Queste soluzioni in genere risiedono nel repository di avvio rapido, repository GitHub privati oppure provengono da impegni dei clienti 1:1 che creano un indirizzo IP durevole, ad esempio un'app distribuibile e scalabile.

Sono necessari diversi passaggi manuali:

1. Creare uno o più GUID da usare come ID di rilevamento.
1. Registrare questi GUID nel centro per i partner.
1. Aggiungere i GUID registrati all'app di Azure e/o alle stringhe agente utente.

### <a name="create-guids"></a>Crea GUID

A differenza degli ID di rilevamento creati da partner Center per conto dell'utente per le app di Azure nel Marketplace commerciale, per altri usi di CUA è necessario creare un GUID da usare come ID di traccia. Un GUID è un identificatore di riferimento univoco con 32 cifre esadecimali. Per creare GUID per il rilevamento, è necessario usare un generatore di GUID, ad esempio tramite PowerShell:

```powershell
[guid]::NewGuid()
```

È necessario creare un GUID univoco per ogni canale di prodotto e di distribuzione. Se non si vuole suddividere i report, è possibile usare un solo GUID per i canali di distribuzione multipli di un prodotto. La creazione di report viene eseguita da Microsoft Partner Network ID e GUID.

### <a name="register-guids"></a>Registrare i GUID

I GUID devono essere registrati successivamente nel centro per i partner, in modo che possano essere associati all'utente come partner:

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard).

1. Eseguire la registrazione come [editore del marketplace commerciale](https://aka.ms/JoinMarketplace).

1. Selezionare **Impostazioni** (icona a forma di ingranaggio) nell'angolo superiore destro, quindi **Impostazioni account**.

1. Selezionare   >  **identificatori** profilo organizzazione  >  **Aggiungi GUID di rilevamento**.

1. Nella casella **GUID** immettere il GUID di verifica. Immettere solo il GUID senza il `pid-` prefisso. Nella casella **Descrizione** immettere il nome o la descrizione della soluzione.

1. Per registrare più GUID, selezionare di nuovo **Add Tracking GUID** (Aggiungi GUID di rilevamento). Nella pagina verranno visualizzate finestre aggiuntive.

1. Selezionare **Salva**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Aggiungere un GUID a un modello di Gestione risorse

Per aggiungere il GUID registrato a un modello di Gestione risorse, apportare una singola modifica al file modello principale:

1. Aprire il modello di Resource Manager.

1. Aggiungere una nuova risorsa di tipo [Microsoft. resources/Deployments](/azure/templates/microsoft.resources/deployments) nel file di modello principale. La risorsa deve trovarsi nella **mainTemplate.js** oazuredeploy.jssolo **su** file, non in tutti i modelli annidati o collegati.

1. Immettere il valore GUID dopo il `pid-` prefisso come nome della risorsa. Se ad esempio il GUID è eb7927c8-dd66-43e1-b0cf-c346a422063, il nome della risorsa sarà **PID-eb7927c8-dd66-43e1-b0cf-c346a422063**. Esempio:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Controllare se nel modello sono presenti errori.

1. Pubblicare nuovamente il modello nel repository appropriato.

1. [Verificare l'esito positivo del GUID nella distribuzione del modello](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Per altre informazioni sulla creazione e la pubblicazione di modelli di Gestione risorse, vedere: [creare e distribuire il primo modello di gestione risorse](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Verificare le distribuzioni registrate con un GUID

Dopo aver modificato il modello ed eseguito una distribuzione di prova, usare lo script di PowerShell seguente per recuperare le risorse distribuite e contrassegnate.

È possibile utilizzare lo script per verificare che il GUID sia stato aggiunto correttamente al modello di Resource Manager. Lo script non si applica alle distribuzioni tramite l'API di Resource Manager o Terraform.

Accedere ad Azure. Selezionare la sottoscrizione con la distribuzione che si vuole verificare prima di eseguire lo script. Eseguire lo script nel contesto della sottoscrizione della distribuzione.

Il **GUID** (sotto denominato "DeploymentName") e il nome **resourceGroupName** della distribuzione sono parametri obbligatori.

È possibile ottenere [lo script originale](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) su GitHub.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Informare i clienti

I partner devono informare i propri clienti in merito alle distribuzioni che usano l'attribuzione dell'utilizzo da parte dei clienti. Microsoft segnala l'utilizzo di Azure associato a queste distribuzioni al partner. Gli esempi seguenti includono i contenuti che è possibile utilizzare per notificare ai clienti queste distribuzioni. Negli esempi sostituire \<PARTNER> con il nome della società. I partner devono garantire che la notifica sia allineata ai criteri di privacy e raccolta dei dati, incluse le opzioni che i clienti devono escludere dal rilevamento.

#### <a name="notification-for-resource-manager-template-deployments"></a>Notifica per le distribuzioni dei modelli di Resource Manager

Quando si distribuisce questo modello, Microsoft è in grado di identificare l'installazione del \<PARTNER> software con le risorse di Azure distribuite. Microsoft può correlare queste risorse utilizzate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. I dati vengono raccolti e regolati dall'Informativa sulla privacy di Microsoft, disponibile all'indirizzo [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Notifica per le distribuzioni di API o SDK

Quando si distribuisce \<PARTNER> il software, Microsoft è in grado di identificare l'installazione del \<PARTNER> software con le risorse di Azure distribuite. Microsoft può correlare queste risorse utilizzate per supportare il software. Microsoft raccoglie queste informazioni per fornire l'esperienza utente migliore con i propri prodotti e per il funzionamento delle proprie attività aziendali. I dati vengono raccolti e regolati dall'Informativa sulla privacy di Microsoft, disponibile all'indirizzo [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Usare API Gestione risorse

In alcuni casi, è possibile effettuare chiamate direttamente alle API REST di Gestione risorse per distribuire i servizi di Azure. [Azure supporta più SDK](../index.yml?pivot=sdkstools) per abilitare queste chiamate. È possibile usare uno degli SDK o chiamare direttamente le API REST per distribuire le risorse.

Per abilitare l'attribuzione dell'utilizzo del cliente, quando si progettano le chiamate API, includere l'ID di traccia nell'intestazione agente utente nella richiesta. Formattare la stringa con il `pid-` prefisso. Esempi:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Se si usano API di Gestione risorse con un'app di Azure nel Marketplace commerciale, usare l'ID di traccia disponibile nel centro per i partner. Non usare un GUID.

Diversi SDK interagiscono con le API Gestione risorse in modo diverso e richiedono alcune differenze nel codice. Gli esempi seguenti presentano l'approccio del Marketplace non commerciale usando un GUID e coprono un'ampia gamma di Azure SDK più diffusi.

#### <a name="example-python-sdk"></a>Esempio: Python SDK

Per Python, usare l’attributo **config**. È possibile aggiungere l’attributo solo a un agente utente. Esempio:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Aggiungere l'attributo per ogni client. Non esiste una configurazione statica globale. È possibile contrassegnare una factory client per assicurarsi che ogni client stia monitorando. Per ulteriori informazioni, vedere questo [esempio di client factory in GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Esempio: .NET SDK

Per .NET, assicurarsi di impostare l'agente utente. Usare la libreria [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) per impostare l'agente utente con il codice seguente, ad esempio in C#:

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Esempio: Azure PowerShell

Se si distribuiscono risorse tramite Azure PowerShell, aggiungere il GUID utilizzando questo metodo:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Esempio: Interfaccia della riga di comando di Azure

Quando si usa l'interfaccia della riga di comando di Azure per aggiungere il GUID, impostare la variabile di ambiente **AZURE_HTTP_USER_AGENT** nell'ambito di uno script. È anche possibile impostare la variabile a livello globale per l'ambito della shell:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Per altre informazioni, vedere [Azure SDK per Go](/azure/developer/go/).

## <a name="use-terraform"></a>Usare Terraform

Il supporto per la bonifica è disponibile tramite la versione 1.21.0 del provider di Azure: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Questo vale per tutti i partner che distribuiscono la propria soluzione tramite la bonifica e tutte le risorse distribuite e a consumo dal provider di Azure (versione 1.21.0 o successiva).

Il provider di Azure per la bonifica ha aggiunto un nuovo campo facoltativo denominato [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) per specificare il GUID di rilevamento usato per la soluzione. Il valore di questo campo può anche essere ricavato dalla variabile di ambiente *ARM_PARTNER_ID*.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Impostare il valore di *partner_id* su un GUID registrato. NON anteporre "PID-" al GUID, basta impostarlo sul GUID effettivo.

> [!IMPORTANT]
> Se si usa la bonifica con un'app di Azure nel Marketplace commerciale, usare l'intero ID di traccia disponibile nel centro per i partner. Non usare un GUID.

## <a name="get-support"></a>Supporto

Per informazioni sulle opzioni di supporto disponibili nel Marketplace commerciale, vedere [supporto per il programma Commercial Marketplace nel centro per i partner](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Come inviare una richiesta di consulenza tecnica

1. Visitare la pagina dei [servizi tecnici dei partner](https://aka.ms/TechnicalJourney).
1. Selezionare **infrastruttura cloud e gestione** per visualizzare il percorso tecnico.
1. Selezionare **Servizi**  >  **di distribuzione invia una richiesta**.
1. Accedere usando il servizio MSA (account MPN) o AAD (account dashboard partner).
1. Completa/controlla le informazioni di contatto nel modulo visualizzato. È possibile che i dettagli della consultazione siano precompilati o che siano disponibili opzioni a discesa.
1. Immettere un titolo e una descrizione dettagliata del problema.
1. Selezionare **Submit** (Invia).

Vedere le istruzioni dettagliate con i relativi screenshot in [Uso di servizi di prevendita e distribuzione tecnici](https://aka.ms/TechConsultInstructions).

Si verrà contattati da un consulente tecnico partner Microsoft per organizzare una chiamata in cui delineare le esigenze.

## <a name="report"></a>Report
La creazione di report per l'utilizzo di Azure rilevati tramite l'attribuzione dell'utilizzo del cliente non è attualmente disponibile per i partner ISV L'aggiunta di report al programma Commercial Marketplace nel centro per i partner per coprire l'attribuzione dell'utilizzo dei clienti è destinata alla seconda metà del 2021.

## <a name="faq"></a>Domande frequenti

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Una volta aggiunto un ID di traccia, è possibile modificarlo?

Gli ID di rilevamento per le app di Azure nel Marketplace commerciale vengono gestiti automaticamente dal centro per i partner. Un cliente può tuttavia scaricare un modello e modificare o rimuovere l'ID di traccia. I partner devono descrivere in modo proattivo il ruolo dell'ID di traccia ai clienti per evitare la rimozione o la modifica. La modifica dell'ID di traccia riguarda solo le nuove distribuzioni e risorse, non quelle esistenti.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>È possibile monitorare i modelli distribuiti da un repository non Microsoft, ad esempio GitHub?

Sì, a condizione che l'ID di rilevamento sia presente durante la distribuzione del modello, viene rilevato l'utilizzo. Per mantenere l'associazione tra l'utente come server di pubblicazione e il modello distribuito da un repository non Microsoft, scaricare prima una copia del modello pubblicato (che conterrà l'ID di traccia) dall'inserzione del Marketplace commerciale dell'offerta nel portale di Azure. Pubblicare la versione in GitHub o in un altro repository non Microsoft.

Se il modello non è elencato nel Marketplace commerciale e include un GUID registrato, assicurarsi che il GUID sia presente nella versione pubblicata in GitHub o in un altro repository non Microsoft.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Il cliente riceve anche la funzione di creazione report?

No. I clienti possono tenere traccia dell'utilizzo di tutte le risorse o dei gruppi di risorse all'interno del portale di Azure. I clienti non visualizzano l'utilizzo suddiviso in base all'ID di traccia CUA.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>L'attribuzione dell'utilizzo del cliente è simile a Digital Partner of record (DPOR) o partner admin link (PAL)?

L'attribuzione dell'utilizzo del cliente è un meccanismo che consente di associare l'utilizzo di Azure con un indirizzo IP ripetibile e distribuibile del partner che costituisce l'associazione al momento della distribuzione. DPOR e PAL hanno lo scopo di associare un partner di consulenza (Systems Integrator) o di gestione (provider di servizi gestiti) con il footprint di Azure pertinente del cliente per il periodo di tempo in cui il partner è impegnato con il cliente.