---
title: Abilitare l'addon di monitoraggio AKS usando criteri di Azure
description: Viene descritto come abilitare l'addon di monitoraggio AKS usando i criteri personalizzati di Azure.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713899"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Abilitare l'addon di monitoraggio AKS usando criteri di Azure
Questo articolo descrive come abilitare l'addon di monitoraggio AKS usando i criteri personalizzati di Azure. Il monitoraggio del criterio personalizzato dell'addon può essere assegnato all'ambito della sottoscrizione o del gruppo di risorse. Se l'area di lavoro di Azure Log Analytics e il cluster AKS si trovano in sottoscrizioni diverse, l'identità gestita usata dall'assegnazione dei criteri deve avere le autorizzazioni necessarie per entrambe le sottoscrizioni o almeno per la risorsa dell'area di lavoro Log Analytics. Analogamente, se il criterio è limitato al gruppo di risorse, l'identità gestita deve avere le autorizzazioni necessarie per il ruolo nell'area di lavoro Log Analytics se l'area di lavoro non è presente nell'ambito del gruppo di risorse selezionato.

Il monitoraggio dell'addon richiede i seguenti ruoli sull'identità gestita usata da criteri di Azure:

 - [Azure-kubernetes-Service-collaboratore-ruolo](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [log-Analytics-collaboratore](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Creare e assegnare la definizione dei criteri usando portale di Azure

### <a name="create-policy-definition"></a>Creare una definizione di criterio

1. Scaricare la definizione dei criteri personalizzati di Azure per abilitare l'addon di monitoraggio AKS.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Passare a https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions e creare la definizione dei criteri con i dettagli seguenti nella finestra di dialogo Definizione criteri creazione.
 
    - **Percorso definizione**: scegliere la sottoscrizione di Azure in cui deve essere archiviata la definizione dei criteri.
    - **Nome**: *(anteprima) AKS-Monitoring-addon*
    - **Descrizione**: *criteri personalizzati di Azure per abilitare il monitoraggio dell'addon nei cluster Kubernetes di Azure nell'ambito specificato*
    - **Categoria**: scegliere *Usa esistente* e selezionare *Kubernetes* dall'elenco a discesa.
    - **Regola dei criteri**: rimuovere le regole di esempio esistenti e copiare il contenuto del *azurepolicy.js* scaricato nel passaggio #1 precedente.

### <a name="assign-policy-definition-to-specified-scope"></a>Assegnare la definizione dei criteri all'ambito specificato

> [!NOTE]
>  L'identità gestita verrà creata automaticamente e i ruoli specificati verranno assegnati nella definizione dei criteri.

1. Selezionare l'addon per la definizione dei criteri *(anteprima) AKS Monitoring* appena creato.
4. Fare clic su Assegna * * e specificare l' **ambito** di *assegnazione* dei criteri. 
5. Fare clic su **Avanti** e specificare l'ID risorsa dell'area di lavoro di Azure log Analytics. Il formato dell'ID risorsa deve essere il seguente `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. Creare un'attività di correzione nel caso in cui si desideri applicare i criteri ai cluster AKS esistenti nell'ambito selezionato.
7. Fare clic su **Verifica + crea** per creare l'assegnazione dei criteri.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Creare e assegnare la definizione dei criteri usando l'interfaccia della riga di comando

### <a name="create-policy-definition"></a>Creare una definizione di criterio

1. Scaricare i file di regole e parametri di definizione dei criteri personalizzati di Azure con i comandi seguenti:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Creare la definizione dei criteri con il comando seguente:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Assegnare la definizione dei criteri all'ambito specificato

- Creare l'assegnazione dei criteri con il comando seguente:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [criteri di Azure](../../governance/policy/overview.md).
- Informazioni sul [funzionamento della sicurezza](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)per la correzione.
- Altre informazioni su [container Insights](./container-insights-overview.md).
- Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).