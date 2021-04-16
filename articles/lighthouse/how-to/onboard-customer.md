---
title: Eseguire l'onboarding dei clienti in Azure Lighthouse
description: Informazioni su come eseguire l'onboarded di un cliente per Azure Lighthouse, consentendo l'accesso e la gestione delle risorse tramite il proprio tenant usando la gestione delle risorse delegate di Azure.
ms.date: 03/29/2021
ms.topic: how-to
ms.openlocfilehash: d8ad448ac022b07ecdea6b68c4544b8c955814b1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497966"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Eseguire l'onboarding dei clienti in Azure Lighthouse

Questo articolo illustra come un provider di servizi può eseguire l'onboard di un cliente per Azure Lighthouse. In questo caso, le risorse delegate (sottoscrizioni e/o gruppi di risorse) nel tenant Azure Active Directory (Azure AD) del cliente possono essere gestite tramite il proprio tenant usando la gestione delle risorse delegate di [Azure.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Anche se si fa riferimento ai provider di servizi e ai clienti in questo argomento, le aziende che gestiscono più [tenant](../concepts/enterprise.md) possono usare lo stesso processo per configurare Azure Lighthouse e consolidare l'esperienza di gestione.

È possibile ripetere il processo di onboarding per più clienti. Quando un utente con le autorizzazioni appropriate accede al tenant di gestione, può essere autorizzato tra gli ambiti di tenancy dei clienti per eseguire operazioni di gestione, senza dover accedere a ogni singolo tenant del cliente.

Per tenere traccia dell'impatto sull'engagement dei clienti e ricevere il riconoscimento, associare l'ID MPN (Microsoft Partner Network) ad almeno un account utente che abbia accesso a ognuna delle sottoscrizioni di cui è stato eseguito l'onboarding. È necessario eseguire questa associazione nel tenant del provider di servizi. È consigliabile creare un account dell'entità servizio nel tenant associato all'ID MPN, includendo quindi l'entità servizio ogni volta che si onboarde un cliente. Per altre informazioni, vedere [Collegare l'ID partner per abilitare il credito ottenuto dal partner nelle risorse delegate.](partner-earned-credit.md)

> [!NOTE]
> I clienti possono essere in alternativa Azure Lighthouse quando acquistano un'offerta di servizio gestito (pubblicata o privata) pubblicata in Azure Marketplace [.](publish-managed-services-offers.md) È anche possibile usare il processo di onboarding descritto qui insieme alle offerte pubblicate Azure Marketplace.

Il processo di onboarding richiede l'esecuzione di azioni sia dal tenant del provider di servizi che dal tenant del cliente. Tutti questi passaggi sono descritti in questo articolo.

## <a name="gather-tenant-and-subscription-details"></a>Raccogliere i dettagli del tenant e della sottoscrizione

Per eseguire l'onboarding del tenant di un cliente, è necessario che abbia una sottoscrizione di Azure attiva. Sarà necessario conoscere quanto segue:

- ID del tenant del provider di servizi, in cui si gestiranno le risorse del cliente
- ID del tenant del cliente, che avrà le risorse gestite dal provider di servizi
- ID di ogni specifica sottoscrizione nel tenant del cliente che verrà gestito dal provider di servizi o che contiene i gruppi di risorse che verranno gestiti dal provider di servizi.

Se i valori ID non sono già disponibili, è possibile recuperarli in uno dei modi seguenti. Assicurarsi di usare questi valori esatti nella distribuzione.

### <a name="azure-portal"></a>Portale di Azure

L'ID del tenant può essere visualizzato passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**. Per selezionare e copiare l'ID tenant, cercare "Azure Active Directory" nel portale, quindi selezionare **Proprietà** e copiare il valore visualizzato nel campo **ID directory**. Per trovare l'ID di una sottoscrizione nel tenant personalizzato, cercare "Sottoscrizioni" e quindi selezionare l'ID della sottoscrizione appropriato.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Quando si esegue l'onboarding di una sottoscrizione (o di uno o più gruppi di risorse all'interno di una sottoscrizione) tramite il processo descritto in questo articolo, il provider di risorse **Microsoft.ManagedServices** verrà registrato per tale sottoscrizione.

## <a name="define-roles-and-permissions"></a>Definire ruoli e autorizzazioni

Il provider di servizi può eseguire più attività per un singolo cliente, per cui deve avere un accesso diverso per ambiti diversi. È possibile definire tutte le autorizzazioni necessarie per assegnare i ruoli predefiniti [di Azure appropriati.](../../role-based-access-control/built-in-roles.md) Ogni autorizzazione include un **principalId** che fa riferimento a un Azure AD, un gruppo o un'entità servizio nel tenant di gestione.

> [!NOTE]
> Se non specificato in modo esplicito, i riferimenti a un "utente" nella documentazione Azure Lighthouse possono essere applicati a un Azure AD utente, gruppo o entità servizio in un'autorizzazione.

Per semplificare la gestione, è consigliabile usare Azure AD gruppi di utenti per ogni ruolo quando possibile, anziché per i singoli utenti. In questo modo è possibile aggiungere o rimuovere singoli utenti al gruppo che ha accesso, in modo che non sia necessario ripetere il processo di onboarding per apportare modifiche agli utenti. È anche possibile assegnare ruoli a un'entità servizio, che può essere utile per gli scenari di automazione.

> [!IMPORTANT]
> Per aggiungere autorizzazioni per un gruppo Azure AD, il **tipo di** gruppo deve essere impostato su **Sicurezza**. Questa opzione è selezionata quando viene creato il gruppo. Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Quando si definiscono le autorizzazioni, assicurarsi di seguire il principio dei privilegi minimi in modo che gli utenti abbiano solo le autorizzazioni necessarie per completare il proprio processo. Per informazioni sui ruoli supportati e sulle procedure consigliate, vedere [Tenant, utenti e](../concepts/tenants-users-roles.md)ruoli Azure Lighthouse scenari .

Per definire le autorizzazioni, è necessario conoscere i valori ID per ogni utente, gruppo di utenti o entità servizio nel tenant del provider di servizi a cui si vuole concedere l'accesso. È anche necessario l'ID di definizione del ruolo per ogni ruolo predefinito che si vuole assegnare. Se non sono già presenti, è possibile recuperarli eseguendo i comandi seguenti dall'interno del tenant del provider di servizi.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> È consigliabile assegnare il [ruolo di eliminazione dell'assegnazione della registrazione di servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) durante l'onboarding di un cliente, in modo che gli utenti nel tenant possano [rimuovere l'accesso alla delega](remove-delegation.md) in un secondo momento se necessario. Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

## <a name="create-an-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager

Per eseguire l'onboarding del cliente, sarà necessario creare un modello di [Azure Resource Manager](../../azure-resource-manager/index.yml) per l'offerta con le informazioni seguenti. I **valori mspOfferName** e **mspOfferDescription** saranno visibili al cliente nella pagina [Provider](view-manage-service-providers.md) di servizi del portale di Azure.

|Campo  |Definizione  |
|---------|---------|
|**mspOfferName**     |Nome che descrive questa definizione. Questo valore viene visualizzato al cliente come titolo dell'offerta e deve essere un valore univoco.        |
|**mspOfferDescription**     |Breve descrizione dell'offerta, ad esempio "Offerta di gestione di macchine virtuali Contoso".      |
|**managedByTenantId**     |ID tenant.          |
|**authorizations**     |Valori **principalId** per gli utenti, i gruppi o i nomi dell'entità servizio del tenant, ognuno dei quali ha un **principalIdDisplayName** per consentire ai clienti di comprendere lo scopo dell'autorizzazione ed è associato a un valore **roleDefinitionId** predefinito per specificare il livello di accesso.      |

Il processo di onboarding richiede un modello di Azure Resource Manager, disponibile nel [repository di esempi](https://github.com/Azure/Azure-Lighthouse-samples/), e un file di parametri corrispondente modificato in modo che corrisponda alla configurazione e definisca le autorizzazioni.

> [!IMPORTANT]
> Il processo descritto qui richiede una distribuzione separata per ogni sottoscrizione di cui viene onboarding, anche se si sta onboarding delle sottoscrizioni nello stesso tenant del cliente. Le distribuzioni separate sono necessarie anche se si esegue l'onboarding di più gruppi di risorse all'interno di sottoscrizioni diverse nello stesso tenant del cliente. Tuttavia, l'onboarding di più gruppi di risorse all'interno di una singola sottoscrizione può essere eseguito in una sola distribuzione.
>
> Le distribuzioni separate sono necessarie anche se si applicano più offerte alla stessa sottoscrizione (o gruppi di risorse all'interno di una sottoscrizione). Ogni offerta applicata deve usare un diverso **mspOfferName**.

Il modello scelto dipenderà dal fatto che si esegua l'onboarding di un'intera sottoscrizione, di un gruppo di risorse o di più gruppi di risorse all'interno di una sottoscrizione. Viene anche fornito un modello che può essere usato per i clienti che hanno acquistato un'offerta di servizio gestito pubblicata in Azure Marketplace, se si preferisce eseguire l'onboarding delle sottoscrizioni in questo modo.

|Onboarding di  |Usare questo modello di Azure Resource Manager  |E modificare questo file dei parametri |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Più gruppi di risorse all'interno di una sottoscrizione   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Sottoscrizione (quando si usa un'offerta pubblicata in Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Anche se non è possibile eseguire l'onboard di un intero gruppo di gestione in una distribuzione, è possibile distribuire un criterio [a livello di gruppo di gestione.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) I criteri utilizzano l'effetto [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) per verificare se ogni sottoscrizione all'interno del gruppo di gestione è stata delegata al tenant di gestione specificato e, in caso contrario, creerà l'assegnazione in base ai valori specificati. Sarà quindi possibile accedere a tutte le sottoscrizioni nel gruppo di gestione, anche se sarà necessario lavorare su di esse come sottoscrizioni singole , anziché eseguire azioni sull'intero gruppo di gestione.

L'esempio seguente illustra un file **delegatedResourceManagement.parameters.json** modificato che potrà essere usato per eseguire l'onboarding di una sottoscrizione. I file di parametri dei gruppi di risorse (disponibili nella cartella [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management)) sono simili, ma includono anche un parametro **rgName** per identificare i gruppi di risorse specifici di cui eseguire l'onboarding.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

L'ultima autorizzazione nell'esempio precedente aggiunge un **principalId** con il ruolo Amministratore Accesso utenti (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Quando si assegna questo ruolo, è necessario includere la **proprietà delegatedRoleDefinitionIds** e uno o più ruoli predefiniti di Azure supportati. L'utente creato in questa autorizzazione sarà [in](../../active-directory/managed-identities-azure-resources/overview.md) grado di assegnare questi ruoli alle identità gestite nel tenant del cliente, operazione necessaria per distribuire criteri che possono [essere corretti.](deploy-policy-remediation.md)  L'utente è anche in grado di creare eventi imprevisti di supporto. A questo **principalId** non verranno applicate altre autorizzazioni normalmente associate al ruolo Amministratore accesso utenti .

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuire i modelli di Azure Resource Manager

Dopo aver aggiornato il file dei parametri, un utente nel tenant del cliente deve distribuire il modello Azure Resource Manager all'interno del tenant. È necessaria una distribuzione separata per ogni sottoscrizione di cui si vuole eseguire l'onboard o per ogni sottoscrizione contenente i gruppi di risorse di cui si vuole eseguire l'onboard.

> [!IMPORTANT]
> Questa distribuzione deve essere eseguita da un account non guest nel tenant del cliente che ha un ruolo con l'autorizzazione , ad esempio Proprietario , per la sottoscrizione di cui viene eseguito l'onboarded o che contiene i gruppi di risorse di cui viene eseguito `Microsoft.Authorization/roleAssignments/write` l'onboarded. [](../../role-based-access-control/built-in-roles.md#owner) Per trovare gli utenti che possono delegare la sottoscrizione, un utente nel tenant del cliente può selezionare la sottoscrizione nel portale di Azure, aprire Controllo di accesso **(IAM)** e visualizzare tutti gli utenti con il ruolo [Proprietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Se la sottoscrizione è stata creata usando il [programma Cloud Solution Provider (CSP)](../concepts/cloud-solution-provider.md), tutti gli utenti con il ruolo [Agente amministratore](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) nel tenant del provider di servizi possono eseguire la distribuzione.

La distribuzione può essere eseguita nel portale di Azure, tramite PowerShell o tramite l'interfaccia della riga di comando di Azure, come illustrato di seguito.

### <a name="azure-portal"></a>Portale di Azure

1. Nel repository [GitHub](https://github.com/Azure/Azure-Lighthouse-samples/)selezionare il pulsante **Distribuisci in Azure** visualizzato accanto al modello che si vuole usare. Il modello verrà aperto nel portale di Azure.
1. Immettere i valori per **Nome offerta Msp,** **Descrizione offerta Msp,** **Gestito dall'ID tenant** e **Autorizzazioni**. Se si preferisce, è possibile selezionare **Modifica** parametri per immettere i valori per `mspOfferName` , e direttamente nel file dei `mspOfferDescription` `managedbyTenantId` `authorizations` parametri. Assicurarsi di aggiornare questi valori anziché usare i valori predefiniti del modello.
1. Selezionare **Rivedi e crea** e quindi **Crea.**

Dopo alcuni minuti verrà visualizzata una notifica che indica che la distribuzione è stata completata.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Verificare che l'onboarding sia riuscito

Quando una sottoscrizione del cliente è stata correttamente onboarded in Azure Lighthouse, gli utenti nel tenant del provider di servizi potranno visualizzare la sottoscrizione e le relative risorse (se gli è stato concesso l'accesso tramite il processo precedente, singolarmente o come membro di un gruppo Azure AD con le autorizzazioni appropriate). Per esserne certi, verificare che la sottoscrizione venga visualizzata in uno dei modi seguenti.  

### <a name="azure-portal"></a>Portale di Azure

Nel tenant del provider di servizi:

1. Passare alla [pagina Clienti personali](view-manage-customers.md).
2. Selezionare **Clienti**.
3. Verificare che sia possibile visualizzare le sottoscrizioni con il nome dell'offerta specificato nel modello di Resource Manager.

> [!IMPORTANT]
> Per visualizzare la sottoscrizione delegata [in](view-manage-customers.md)Clienti, agli utenti nel tenant del [](../../role-based-access-control/built-in-roles.md#reader) provider di servizi deve essere stato concesso il ruolo Lettore (o un altro ruolo predefinito che include l'accesso come lettore) al momento dell'onboarded della sottoscrizione.

Nel tenant del cliente:

1. Passare alla [pagina Provider di servizi](view-manage-service-providers.md).
2. Selezionare **Offerte del provider di servizi**.
3. Verificare che sia possibile visualizzare le sottoscrizioni con il nome dell'offerta specificato nel modello di Resource Manager.

> [!NOTE]
> Potrebbero essere necessari fino a 15 minuti dopo il completamento della distribuzione prima che gli aggiornamenti si riflettano nel portale di Azure. È possibile visualizzare gli aggiornamenti prima se si aggiorna il token Azure Resource Manager aggiornando il browser, accedendo e disconnessione o richiedendo un nuovo token.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Se è necessario apportare modifiche dopo l'onboarded del cliente, è possibile [aggiornare la delega](update-delegation.md). È anche possibile [rimuovere completamente l'accesso alla](remove-delegation.md) delega.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se non è possibile eseguire correttamente l'onboarding del cliente o se gli utenti hanno problemi ad accedere alle risorse delegate, controllare i suggerimenti e i requisiti seguenti e riprovare.

- Il `managedbyTenantId` valore non deve corrispondere all'ID tenant per la sottoscrizione di cui è in corso l'onboarded.
- Non è possibile avere più assegnazioni nello stesso ambito con lo stesso `mspOfferName` .
- Il provider **di risorse Microsoft.ManagedServices** deve essere registrato per la sottoscrizione delegata. Questa operazione dovrebbe verificarsi automaticamente durante la distribuzione, ma in caso contrario, è possibile [registrarla manualmente.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)
- Le autorizzazioni non devono includere utenti con il ruolo predefinito [Proprietario](../../role-based-access-control/built-in-roles.md#owner) o i ruoli predefiniti con [DataActions](../../role-based-access-control/role-definitions.md#dataactions).
- I gruppi devono essere creati con [**Tipo di gruppo**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) impostato su **Sicurezza** e **non Microsoft 365**.
- Potrebbe verificarsi un ritardo aggiuntivo prima che l'accesso venga abilitato per i [gruppi annidati.](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)
- Gli utenti che devono visualizzare le risorse nel portale di Azure devono avere il ruolo [Lettore](../../role-based-access-control/built-in-roles.md#reader) (o un altro ruolo predefinito che include l'accesso lettore).
- I [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md) inclusi nelle autorizzazioni non devono includere ruoli deprecati. Se un ruolo predefinito di Azure diventa deprecato, tutti gli utenti che sono stati onboarded con tale ruolo perderanno l'accesso e non sarà possibile eseguire l'onboarded di deleghe aggiuntive. Per risolvere il problema, aggiornare il modello in modo che usi solo i ruoli predefiniti supportati, quindi eseguire una nuova distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
- Informazioni su come [aggiornare o](update-delegation.md) [rimuovere una](remove-delegation.md) delega.
