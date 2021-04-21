---
title: Azure Key Vault spostamento di un insieme di credenziali in un'altra sottoscrizione | Microsoft Docs
description: Indicazioni sullo spostamento di un insieme di credenziali delle chiavi in una sottoscrizione diversa.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 1a1cd8c051f9e04c09ef2986805873d8e7fea54e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817631"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Spostamento di Azure Key Vault in un'altra sottoscrizione

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

> [!IMPORTANT]
> **Lo spostamento di un insieme di credenziali delle chiavi in un'altra sottoscrizione causerà una modifica di rilievo nell'ambiente.**
> Assicurarsi di comprendere l'impatto di questa modifica e seguire attentamente le indicazioni riportate in questo articolo prima di decidere di spostare l'insieme di credenziali delle chiavi in una nuova sottoscrizione.
> Se si usano identità del servizio gestite (MSI), leggere le istruzioni post-spostamento alla fine del documento. 

[Azure Key Vault](overview.md) viene associato automaticamente all'ID tenant [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) predefinito per la sottoscrizione in cui viene creato. È possibile trovare l'ID tenant associato alla sottoscrizione seguendo questa [guida.](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md) Anche tutte le voci dei criteri di accesso e le assegnazioni di ruoli sono collegate a questo ID tenant.  Se si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non saranno accessibili dalle entità servizio (utenti e applicazioni) nel tenant B. Per risolvere questo problema, è necessario:

* Modificare l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione impostandolo sul tenant B.
* Rimuovere tutte le voci dei criteri di accesso esistenti.
* Aggiungere nuove voci dei criteri di accesso associate al tenant B.

Per altre informazioni sui Azure Key Vault e Azure Active Directory, vedere
- [Informazioni su Azure Key Vault](overview.md)
- [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Come trovare l'ID tenant](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Limitazioni

> [!IMPORTANT]
> **Gli insiemi di credenziali delle chiavi usati per la crittografia del disco non possono essere spostati** Se si usa l'insieme di credenziali delle chiavi con la crittografia del disco per una macchina virtuale, l'insieme di credenziali delle chiavi non può essere spostato in un gruppo di risorse diverso o in una sottoscrizione mentre la crittografia del disco è abilitata. È necessario disabilitare la crittografia del disco prima di spostare l'insieme di credenziali delle chiavi in un nuovo gruppo di risorse o sottoscrizione. 

Alcune entità servizio (utenti e applicazioni) sono associate a un tenant specifico. Se si sposta l'insieme di credenziali delle chiavi in una sottoscrizione in un altro tenant, è possibile che non sia possibile ripristinare l'accesso a un'entità servizio specifica. Verificare che tutte le entità servizio essenziali esistano nel tenant in cui si sposta l'insieme di credenziali delle chiavi.

## <a name="prerequisites"></a>Prerequisiti

* [Accesso](../../role-based-access-control/built-in-roles.md#contributor) a livello di collaboratore o superiore alla sottoscrizione corrente in cui è presente l'insieme di credenziali delle chiavi. È possibile assegnare il ruolo [usando](../../role-based-access-control/role-assignments-portal.md)portale di Azure, l'interfaccia della riga [di comando](../../role-based-access-control/role-assignments-cli.md)di Azure o [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* [Accesso](../../role-based-access-control/built-in-roles.md#contributor) a livello di collaboratore o superiore alla sottoscrizione in cui si vuole spostare l'insieme di credenziali delle chiavi. È possibile assegnare un ruolo [usando](../../role-based-access-control/role-assignments-portal.md)portale di Azure , l'interfaccia [della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)o [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* Un gruppo di risorse nella nuova sottoscrizione. È possibile crearne uno [usando](../../azure-resource-manager/management/manage-resource-groups-portal.md)portale di Azure , [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)o l'interfaccia della riga [di comando di Azure.](../../azure-resource-manager/management/manage-resource-groups-cli.md)

È possibile controllare i ruoli esistenti usando [portale di Azure,](../../role-based-access-control/role-assignments-list-portal.md) [PowerShell,](../../role-based-access-control/role-assignments-list-powershell.md)l'interfaccia della riga [di comando](../../role-based-access-control/role-assignments-list-cli.md)di Azure o [l'API REST.](../../role-based-access-control/role-assignments-list-rest.md)


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Spostamento di un insieme di credenziali delle chiavi in una nuova sottoscrizione

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Passare all'insieme di [credenziali delle chiavi](overview.md)
3. Fare clic sulla scheda "Panoramica"
4. Selezionare il pulsante "Sposta"
5. Selezionare "Sposta in un'altra sottoscrizione" dall'elenco a discesa
6. Selezionare il gruppo di risorse in cui si vuole spostare l'insieme di credenziali delle chiavi
7. Confermare l'avviso relativo al trasferimento delle risorse
8. Scegliere "OK"

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Passaggi aggiuntivi quando la sottoscrizione si trova in un nuovo tenant

Se l'insieme di credenziali delle chiavi è stato spostato in una sottoscrizione in un nuovo tenant, è necessario aggiornare manualmente l'ID tenant e rimuovere i criteri di accesso e le assegnazioni di ruolo precedente. Ecco le esercitazioni per questi passaggi in PowerShell e nell'interfaccia della riga di comando di Azure. Se si usa PowerShell, potrebbe essere necessario eseguire il comando Clear-AzContext documentato di seguito per consentire di visualizzare le risorse esterne all'ambito selezionato corrente. 

### <a name="update-tenant-id-in-a-key-vault"></a>Aggiornare l'ID tenant in un insieme di credenziali delle chiavi

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Aggiornare i criteri di accesso e le assegnazioni di ruolo

> [!NOTE]
> Se Key Vault usa il modello di autorizzazione controllo degli accessi in base al ruolo di [Azure.](../../role-based-access-control/overview.md) È anche necessario rimuovere le assegnazioni di ruolo dell'insieme di credenziali delle chiavi. È possibile rimuovere le assegnazioni di ruolo usando il portale [di Azure,](../../role-based-access-control/role-assignments-portal.md) [l'interfaccia della riga di](../../role-based-access-control/role-assignments-cli.md)comando di Azure o [PowerShell.](../../role-based-access-control/role-assignments-powershell.md) 

Ora che l'insieme di credenziali è associato all'ID tenant corretto e le voci dei criteri di accesso o le assegnazioni di ruolo precedenti vengono rimosse, impostare nuove voci dei criteri di accesso o assegnazioni di ruolo.

Per l'assegnazione di criteri, vedere:
- [Assegnare un criterio di accesso tramite il portale](assign-access-policy-portal.md)
- [Assegnare criteri di accesso tramite l'interfaccia della riga di comando di Azure](assign-access-policy-cli.md)
- [Assegnare criteri di accesso tramite PowerShell](assign-access-policy-powershell.md)

Per aggiungere assegnazioni di ruolo, vedere:
- [Assegnare ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
- [Assegnare ruoli di Azure tramite l'interfaccia della riga di comando di Azure](../../role-based-access-control/role-assignments-cli.md)
- [Assegnare ruoli di Azure con PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Aggiornare le identità gestite

Se si trasferisce l'intera sottoscrizione e si usa un'identità gestita per le risorse di Azure, è necessario aggiornarla anche al nuovo tenant Azure Active Directory database. Per altre informazioni sulle identità gestite, vedere [Panoramica delle identità gestite.](../../active-directory/managed-identities-azure-resources/overview.md)

Se si usa l'identità gestita, sarà anche necessario aggiornare l'identità perché l'identità precedente non sarà più nel tenant Azure Active Directory corretto. Per risolvere il problema, vedere i documenti seguenti. 

* [Aggiornamento dell'msi](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Trasferire la sottoscrizione a una nuova directory](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni su [chiavi, segreti e certificati](about-keys-secrets-certificates.md)
- Per informazioni concettuali, incluso come interpretare Key Vault log, vedere Key Vault [registrazione](logging.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
- [Azure Key Vault funzionalità di sicurezza](security-features.md)
- [Configurare reti virtuali e firewall di Azure Key Vault](network-security.md)