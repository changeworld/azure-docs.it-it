---
title: Configurare un'istanza e l'autenticazione (PowerShell)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure usando Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044272"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Configurare un'istanza e l'autenticazione di Azure Digital Twins (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins**, inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo descrive manualmente questa procedura, una alla volta, usando [Azure PowerShell](/powershell/azure/new-azureps-module-az).

* Per eseguire questa procedura manualmente usando il portale di Azure, vedere la versione del portale di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (portale)*](how-to-set-up-instance-portal.md).
* Per eseguire un'installazione automatica usando uno script di distribuzione di esempio, vedere la versione con script di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Per prima cosa, scegliere dove eseguire i comandi in questo articolo. È possibile scegliere di eseguire Azure PowerShell comandi utilizzando un'installazione locale di Azure PowerShell o in una finestra del browser utilizzando [Azure cloud Shell](https://shell.azure.com).
    * Se si sceglie di usare Azure PowerShell in locale:
       1. [Installare il modulo Az di PowerShell](/powershell/azure/install-az-ps).
       1. Aprire una finestra di PowerShell nel computer.
       1. Connettersi all'account Azure con il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
    * Se si sceglie di usare Azure Cloud Shell:
        1. Per ulteriori informazioni sui Cloud Shell, vedere [Panoramica delle Azure cloud Shell](../cloud-shell/overview.md) .
        1. Aprire una finestra di Cloud Shell seguendo [questo collegamento](https://shell.azure.com) nel browser.
        1. Nella barra delle icone Cloud Shell verificare che il Cloud Shell sia impostato in modo da eseguire la versione di PowerShell.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell finestra che mostra la selezione della versione di PowerShell":::
    
1. Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare una sottoscrizione specifica usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Se è la prima volta che si usa i dispositivi gemelli digitali di Azure con questa sottoscrizione, è necessario registrare il provider di risorse **Microsoft. DigitalTwins** . In caso di dubbi, è possibile eseguirlo di nuovo anche se è stato già completato nel passato.

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. Usare il comando seguente per installare il modulo di PowerShell **AZ. DigitalTwins** .
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> Mentre il modulo di PowerShell **AZ. DigitalTwins** è in anteprima, è necessario installarlo separatamente usando il `Install-Module` cmdlet, come descritto in precedenza. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile per impostazione predefinita all'interno di Azure Cloud Shell.

## <a name="create-the-azure-digital-twins-instance"></a>Creare l'istanza di Azure Digital Twins

In questa sezione si creerà **una nuova istanza di Azure Digital Twins** usando Azure PowerShell.
È necessario fornire:

* Un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) in cui verrà distribuita l'istanza. Se non si dispone già di un gruppo di risorse esistente, è possibile crearne uno usando il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Area per la distribuzione. Per visualizzare le aree che supportano i dispositivi gemelli digitali di Azure, visitare i [*prodotti Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Nome dell'istanza. Il nome della nuova istanza deve essere univoco all'interno dell'area per la sottoscrizione. Se la sottoscrizione ha un'altra istanza di Azure Digital gemelli nell'area che usa già il nome specificato, verrà chiesto di selezionare un nome diverso.

Usare i valori nel comando seguente per creare l'istanza:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verificare l'esito positivo e raccogliere i valori importanti

Se l'istanza è stata creata correttamente, il risultato sarà simile all'output seguente contenente le informazioni sulla risorsa creata:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

A questo punto, visualizzare le proprietà della nuova istanza eseguendo e inviando il `Get-AzDigitalTwinsInstance` pipe a, come indicato di seguito `Select-Object -Property *` :

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> È possibile usare questo comando per visualizzare tutte le proprietà dell'istanza in qualsiasi momento.

Prendere nota dei nomi **host**, **nome** e **ResourceGroup** dell'istanza di Azure Digital gemelli. Si tratta di valori importanti che potrebbero essere necessari quando si continua a usare l'istanza di Azure Digital Twins, per configurare l'autenticazione e le risorse di Azure correlate. Se altri utenti eseguiranno la programmazione in base all'istanza, è necessario condividerli con questi valori.

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso. Successivamente, si forniranno le autorizzazioni utente di Azure appropriate per gestirlo.

## <a name="set-up-user-access-permissions"></a>Configurare le autorizzazioni di accesso utente

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Per prima cosa, determinare il valore **ObjectID** per l'account Azure ad dell'utente a cui deve essere assegnato il ruolo. È possibile trovare questo valore usando il cmdlet [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) , passando il nome dell'entità utente nell'account Azure ad per recuperare il relativo ObjectId (e altre informazioni utente). Nella maggior parte dei casi, il nome dell'entità utente corrisponderà al messaggio di posta elettronica dell'utente per l'account Azure AD.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Usare quindi il valore **ObjectID** nel comando seguente per assegnare il ruolo. Il comando richiede anche di immettere lo stesso ID sottoscrizione, il nome del gruppo di risorse e il nome dell'istanza di Azure Digital gemelli scelto in precedenza durante la creazione dell'istanza. Il comando deve essere eseguito da un utente con [autorizzazioni sufficienti](#prerequisites-permission-requirements) nella sottoscrizione di Azure.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Il risultato di questo comando è l'output delle informazioni sull'assegnazione di ruolo creata.

### <a name="verify-success"></a>Verificare l'esito positivo

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso e sono state assegnate le autorizzazioni per gestirla.

## <a name="next-steps"></a>Passaggi successivi

Vedere come connettere un'applicazione client all'istanza con il codice di autenticazione:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)
