---
title: Eseguire la migrazione all'anteprima Premium di Azure firewall
description: Informazioni su come eseguire la migrazione da Azure firewall standard all'anteprima Premium di Azure firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549713"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Eseguire la migrazione all'anteprima Premium di Azure firewall

È possibile eseguire la migrazione di Azure firewall standard all'anteprima Premium di Azure firewall per sfruttare le nuove funzionalità Premium. Per altre informazioni sulle funzionalità di anteprima di Azure Firewall Premium, vedere [funzionalità di anteprima di Azure Firewall Premium](premium-features.md).

I due esempi seguenti illustrano come:
- Eseguire la migrazione di un criterio standard esistente usando Azure PowerShell
- Eseguire la migrazione di un firewall standard esistente (con regole classiche) ad Azure Firewall Premium con criteri Premium.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Eseguire la migrazione di un criterio esistente usando Azure PowerShell

`Transform-Policy.ps1` è uno script Azure PowerShell che crea nuovi criteri Premium da un criterio standard esistente.

Dato un ID criterio del firewall standard, lo script lo trasforma in un criterio del firewall Premium di Azure. Lo script si connette prima di tutto al proprio account Azure, estrae il criterio, trasforma/aggiunge diversi parametri e quindi carica un nuovo criterio Premium. I nuovi criteri Premium sono denominati `<previous_policy_name>_premium` .

Esempio d'uso:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Lo script non esegue la migrazione delle impostazioni di intelligence per le minacce. È necessario prendere nota di queste impostazioni prima di procedere e migrarle manualmente.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Eseguire la migrazione di un firewall standard esistente usando il portale di Azure

Questo esempio illustra come usare la portale di Azure per eseguire la migrazione di un firewall standard (regole classiche) ad Azure Firewall Premium con criteri Premium.

1. Dal portale di Azure selezionare il firewall standard. Nella pagina **Overview** selezionare **migrate to Firewall Policy**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Esegui migrazione a criteri firewall":::

1. Nella pagina **migrate to Firewall Policy** selezionare **Review + create**.
1. Selezionare **Crea**.

   Il completamento della distribuzione richiede alcuni minuti.
1. Usare lo `Transform-Policy.ps1` [script Azure PowerShell](#migrate-an-existing-policy-using-azure-powershell) per trasformare questo nuovo criterio standard in un criterio Premium.
1. Nel portale selezionare la risorsa firewall standard. 
1. In **automazione** selezionare **Esporta modello**. Mantieni aperta la scheda del browser. Si tornerà in seguito.
   > [!TIP]
   > Per assicurarsi che il modello non venga perso, scaricarlo e salvarlo nel caso in cui la scheda del browser venga chiusa o aggiornata.
1. Aprire una nuova scheda del browser, passare alla portale di Azure e aprire il gruppo di risorse che contiene il firewall.
1. Eliminare l'istanza del firewall standard esistente.

   Per il completamento dell'operazione sono richiesti alcuni minuti.

1. Tornare alla scheda Esplorazione con il modello esportato.
1. Selezionare **Distribuisci** e quindi nella pagina **distribuzione personalizzata** Selezionare **modifica modello**.
1. Modificare il testo del modello:
   
   1. Sotto la `Microsoft.Network/azureFirewalls` risorsa, in `Properties` , `sku` modificare `tier` da "standard" a "Premium".
   1. Nel modello `Parameters` modificare `defaultValue` per `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` da:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      in:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Selezionare **Salva**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

Al termine della distribuzione, è ora possibile configurare tutte le nuove funzionalità di anteprima di Azure Firewall Premium.

## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più sulle funzionalità Premium di Azure firewall](premium-features.md)